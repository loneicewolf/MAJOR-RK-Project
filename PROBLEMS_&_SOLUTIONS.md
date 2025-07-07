# Problem: NO SYS FILE generated *despite* __no__ errors?

Reason: linking problem,it never linked, and there is no way(**Bug? as usual with windows!**) to do it via gui!

```
<TargetExt>.sys</TargetExt>
<LinkCompiled>true</LinkCompiled>
<TargetPath>$(OutDir)$(TargetName)$(TargetExt)</TargetPath>
<SignMode>None</SignMode>
<GenerateDriverPackage>false</GenerateDriverPackage>
```

```
<IgnoreAllDefaultLibraries>true</IgnoreAllDefaultLibraries>
<AdditionalDependencies>ntoskrnl.lib;hal.lib</AdditionalDependencies>
```
