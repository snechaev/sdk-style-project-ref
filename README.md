# sdk-style-project-ref
Sample solution for SO question, which demonstrates that `ProjectReference` in SDK-style project ignores `Condition` for transitive dependencies

Here we have solution, consisting of 3 projects
* `_TestSDK` - main project (SDK-style, net48)
* `TestTool` - library project (non-SDK-style, net FW4.8)
* `PSWrapper` - library project (non-SDK-style, net FW4.8)

Dependencies are following
* `_TestSDK` references `TestTool`
* `TestTool` *conditionally* references `PSWrapper` (only for `DebugJS` configuration)
```
  <ProjectReference Include="..\PSWrapper\PSWrapper.csproj" Condition="'$(Configuration)'=='DebugJS'">
    <Project>{1675096e-bf7b-486b-89f9-dfe147418dbc}</Project>
    <Name>PSWrapper</Name>
  </ProjectReference>
```
Additionally, in the Configuration manager, the `PSWrapper` project is configured to be built only in `DebugJS` configuration.

The problem is that I can't build `_TestSDK` project in a `Release` configuration in Visual studio 2019 (16.10.2). I got an error 

```
2>CSC : error CS0006: Metadata file 'D:\project\PSWrapper\bin\Release\PSWrapper.dll' could not be found
```

Althought in `Release` configuration `PSWrapper` project should not be used in any way.

If I build `PSWrapper` manually and then try to build `_TestSDK` again, the build will succeed, but in the output directory there will be `PSWrapper.dll`, which should not be there for `Release` configuration.

I created old-style project `_TestFW`, which is just references `TestTool` project (exactly as `_TestSDK` does). And for the `_TestFW` all works fine.

So, the question is: is it a way to force sdk-style project to respect `Condition` attribute for transitive references? 

Note: converting `TestTool` & `PSWrapper` projects to sdk-style is not an option for now.
