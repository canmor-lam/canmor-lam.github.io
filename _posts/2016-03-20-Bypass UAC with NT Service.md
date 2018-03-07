---
layout: post
title:  Bypass UAC with NT Service
categories: code
tags: UAC
---

UAC is introduced since Windows Vista, and is enabled by default. Many operations of application require privileges from administrator, at least one times. If you get a NT service run on background already, here's a way to bypass UAC.

```C++
#include "Wtsapi32.h"
#include "Userenv.h"

namespace ntservice_execute_helper
{
    bool set_privilege(LPCWSTR name, bool enabled)
    {
        bool result = false;
        HANDLE token = 0; 
        if (OpenProcessToken(GetCurrentProcess(), TOKEN_ADJUST_PRIVILEGES | TOKEN_QUERY, &token)) {
            TOKEN_PRIVILEGES state = { 0 };
            state.PrivilegeCount = 1;
            state.Privileges[0].Attributes = enabled ? SE_PRIVILEGE_ENABLED : 0;
            if (LookupPrivilegeValueW(0, name, (PLUID)state.Privileges))
                result = AdjustTokenPrivileges(token, 0, &state, 0, 0, 0) != FALSE;
            CloseHandle(token);
        }
        return result;
    }

    HANDLE acquire_token()
    {
        HANDLE new_token = 0;
        HANDLE old_token = 0;
        OpenProcessToken(GetCurrentProcess(), TOKEN_ALL_ACCESS, &old_token);
        DuplicateTokenEx(old_token, MAXIMUM_ALLOWED, 0, SecurityIdentification, TokenPrimary, &new_token);
        CloseHandle(old_token);
        return new_token;
    }

    DWORD enemerate_an_active_session()
    {
        PWTS_SESSION_INFO sessions_info = 0;
        DWORD count = 0;
        WTSEnumerateSessionsW(0, 0, 1, &sessions_info, &count);
        for (size_t i = 0; i < count; ++i) {
            if (sessions_info[i].State == WTSActive) {
                return sessions_info[i].SessionId;
            }
        }
        return 0;
    }

    HANDLE launch(LPCTSTR path, LPCTSTR cmd, bool wait)
    {
        /* seams not necessary */
        set_privilege(TEXT("SeTcbPrivilege"), true);
        set_privilege(TEXT("SeChangeNotifyPrivilege"), true);
        set_privilege(TEXT("SeIncreaseQuotaPrivilege"), true);
        set_privilege(TEXT("SeAssignPrimaryTokenPrivilege"), true);

        HANDLE token = acquire_token();
        DWORD session_id = enemerate_an_active_session();
        if (!session_id)
            return INVALID_HANDLE_VALUE;

        SetTokenInformation(token, TokenSessionId, &session_id, sizeof(session_id));
        LPVOID environment = NULL;
        CreateEnvironmentBlock(&environment, token, 0);
        STARTUPINFO startup_info = { sizeof(STARTUPINFO) };
        startup_info.lpDesktop = TEXT("WinSta0\\Default");
        PROCESS_INFORMATION process_info = { 0 };

        CreateProcessAsUser(
            token,
            path,
            (LPWSTR)cmd,
            0, 0, 0,
            CREATE_UNICODE_ENVIRONMENT | NORMAL_PRIORITY_CLASS | CREATE_NEW_CONSOLE,
            environment,
            0,
            &startup_info,
            &process_info);

        CloseHandle(token);

        return process_info.hProcess;
    }
}
```