---
layout: post
title: '使用NuGet包不提交到源代码控制'
date: 2012-9-19 10:10
micolog_id: 27001
comments: false
---
参考：

&lt;ol&gt;
&lt;li&gt;http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages&lt;/li&gt;
&lt;li&gt;http://blog.nuget.org/20120518/package-restore-and-consent.html&lt;/li&gt;
&lt;li&gt;http://stackoverflow.com/questions/11261310/nuget-dont-see-allow-nuget-to-download-missing-packages-during-build&lt;/li&gt;
&lt;/ol&gt;
错误：error : Package restore is disabled by default. To give consent, open the Visual Studio Options dialog, click on Package Manager node and check &#39;Allow NuGet to download missing packages during build.&#39; You can also give consent by setting the environment variable &#39;EnableNuGetPackageRestore&#39; to &#39;true&#39;.

解决：提示在环境变量中新增一个变量： &lt;strong&gt;EnableNuGetPackageRestore = true&lt;/strong&gt;

&amp;nbsp;

手动改变配置：

1..sln主项目中新增：

Project(&quot;{2150E333-8FDC-42A3-9474-1A3956D46DE8}&quot;) = &quot;.nuget&quot;, &quot;.nuget&quot;, &quot;{07BDA4AF-B93E-4F33-9E09-2FCA670A0A98}&quot;&lt;br /&gt; &amp;nbsp;&amp;nbsp;&amp;nbsp; ProjectSection(SolutionItems) = preProject&lt;br /&gt; &amp;nbsp;&amp;nbsp;&amp;nbsp; &amp;nbsp;&amp;nbsp;&amp;nbsp; .nuget\NuGet.Config = .nuget\NuGet.Config&lt;br /&gt; &amp;nbsp;&amp;nbsp;&amp;nbsp; &amp;nbsp;&amp;nbsp;&amp;nbsp; .nuget\NuGet.exe = .nuget\NuGet.exe&lt;br /&gt; &amp;nbsp;&amp;nbsp;&amp;nbsp; &amp;nbsp;&amp;nbsp;&amp;nbsp; .nuget\NuGet.targets = .nuget\NuGet.targets&lt;br /&gt; &amp;nbsp;&amp;nbsp;&amp;nbsp; EndProjectSection&lt;br /&gt; EndProject

2.拷贝.nuget目录包含3文件(nuget.config|exe|targets)

3.子项目文件.csproj新增：

&amp;lt;Project ....&lt;br /&gt; &amp;nbsp; &amp;lt;PropertyGroup&amp;gt;&lt;strong&gt;&lt;br /&gt; &amp;nbsp;&amp;nbsp;&amp;nbsp; &amp;lt;SolutionDir Condition=&quot;$(SolutionDir) == &#39;&#39; Or $(SolutionDir) == &#39;*Undefined*&#39;&quot;&amp;gt;..\&amp;lt;/SolutionDir&amp;gt;&lt;br /&gt; &amp;nbsp;&amp;nbsp;&amp;nbsp; &amp;lt;RestorePackages&amp;gt;true&amp;lt;/RestorePackages&amp;gt;&lt;/strong&gt;&lt;br /&gt; &amp;nbsp; &amp;lt;/PropertyGroup&amp;gt;&lt;br /&gt; &lt;strong&gt;&amp;nbsp; &amp;lt;Import Project=&quot;$(SolutionDir)\.nuget\nuget.targets&quot; /&amp;gt;&lt;/strong&gt;&lt;br /&gt; .....&lt;br /&gt; &amp;lt;/Project&amp;gt;
