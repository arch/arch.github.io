---
layout: default
---

I recently ran into a situation where I needed to have a single project in Visual Studio to reference different platform DLL (e.g. x86, x64). 
That's to say, if I build solution with x86 platform the project will reference the x86 dependency library; otherwise, reference the x64 lib.

![conditional library reference]({{ site.url }}/images/conditional-lib-ref.PNG)

## How to do it

In this case, you cannot through right click project's **References** to add the dependency libraries. Conditional library references in visual studio
must through editing the project file **yourprojectname_.csproj**. So, right click project and select **Unload project**, and then right click the unload project again,
from the context menu, select **Edit _yourprojectname_.csproj**, edit project file content as following:

	<ItemGroup>
    <Reference Include="ManagedONS" Condition="'$(Platform)'=='x86'">
      <HintPath>..\Ons\x86\ManagedONS.dll</HintPath>
    </Reference>
    <Reference Include="ManagedONS" Condition="'$(Platform)'=='x64'">
      <HintPath>..\Ons\x64\ManagedONS.dll</HintPath>
    </Reference>
    <Reference Include="ManagedONS" Condition="'$(Platform)'=='AnyCPU'">
      <HintPath>..\Ons\x86\ManagedONS.dll</HintPath>
    </Reference>
    <Reference Include="System" />
    <Reference Include="System.Core" />
    <Reference Include="System.Data" />
  </ItemGroup>
  
In above code, we use `$(Platform)` macro to check the condition, so we also need to configure the platform information through _BUILD_ menu, and than _Configuration Manager_

## Copy the indirect dependency libraries to target location

Sometimes, the project may refereence to a library, but that lib dependency an other library. In this case, because your project doesn't direct ref it (here indirect ref), 
when you build your project, the indirect referenced library will not be copied to target location. However, we can use the **Post-build Event Command Line** to do it:

	if "$(Platform)" == "x86" copy "$(SolutionDir)Ons\x86\*" "$(TargetDir)"
	if "$(Platform)" == "x64" copy "$(SolutionDir)Ons\x64\*" "$(TargetDir)"
	if "$(Platform)" == "AnyCPU" copy "$(SolutionDir)Ons\x86\*" "$(TargetDir)"