---
layout: post
title: '使用NuGet包不提交到源代码控制'
date: 2012-9-19 10:10
micolog_id: 27001
comments: false
categories: tech
---
参考：

- http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages
- http://blog.nuget.org/20120518/package-restore-and-consent.html
- http://stackoverflow.com/questions/11261310/nuget-dont-see-allow-nuget-to-download-missing-packages-during-build

错误：error : Package restore is disabled by default. To give consent, open the Visual Studio Options dialog, click on Package Manager node and check &#39;Allow NuGet to download missing packages during build.&#39; You can also give consent by setting the environment variable &#39;EnableNuGetPackageRestore&#39; to &#39;true&#39;.

解决：提示在环境变量中新增一个变量：

**EnableNuGetPackageRestore = true**


手动改变配置：

1..sln主项目中新增：

	Project("{2150E333-8FDC-42A3-9474-1A3956D46DE8}") = ".nuget", ".nuget", "{07BDA4AF-B93E-4F33-9E09-2FCA670A0A98}"
		ProjectSection(SolutionItems) = preProject
			.nuget\NuGet.Config = .nuget\NuGet.Config
			.nuget\NuGet.exe = .nuget\NuGet.exe
			.nuget\NuGet.targets = .nuget\NuGet.targets
		EndProjectSection
	EndProject


2.拷贝.nuget目录包含3文件(nuget.config|exe|targets)


3.子项目文件.csproj新增：

	<Project ....
	  <PropertyGroup>
		**<SolutionDir Condition="$(SolutionDir) == '' Or $(SolutionDir) == '*Undefined*'">..\</SolutionDir>**
		**<RestorePackages>true</RestorePackages>**
	  </PropertyGroup>
	  **<Import Project="$(SolutionDir)\.nuget\nuget.targets" />**
		.....
	</Project>
	