# Unity Azure Pipeline
*A sample yaml configuration for creating a headless pipeline to build Unity3D projects for HoloLens2 with Azure Pipelines and Microsoft Hosted Agents.*

This documentation will guide you on how to set up your pipelines and get started. 

## Prerequisites: 

 - [x] **Azure DevOps** Organization/Tenant

 - [x] **Microsoft Account** or a work/organization account

 - [x] Knowledge how to add users to your Microsoft Azure Active Directory Tenant (if you plan to collaborate)

 - [x] Knowledge of how to ensure the **Mixed Reality Toolkit** (MRTK) foundation package is added to a Unity project *(**Release v2.1.0** at the time of this writing)*

 - [x] A **Unity 2019** *(2019.2.x at the time of this writing)* project with **MRTK Foundation added**

 - [x] A **Unity Plus/Pro license** serial key with at least 1 available seat to activate. 

**If using Command Line for source control:**

- Git command line package for your platform installed
- The right Git Credential Manager or have configured SSH authentication

## Stand up a new Azure DevOps Environment

Navigate to https://dev.azure.com to sign in and create your project.

When you click the "+New project" button, you may choose your own name, description, visibility privacy (e.g. Public, Private, or Enterprise), version control (e.g. Git), and work item process (e.g. Agile).

## Install the extension ["Unity Tools for Azure DevOps"](https://dinomite-studios.github.io/unity-azure-pipelines-tasks/) 

Once you have your fresh work area created, please install the extension ["Unity Tools for Azure DevOps"](https://dinomite-studios.github.io/unity-azure-pipelines-tasks/) for use in your Azure DevOps organization. Instructions are available by following the link above.

The extension supports Microsoft Hosted Agents as well as Custom Agents.

***Note: A Unity Plus/Pro seat with at least one available activation is required to build using Hosted Agents,** which is what this document describes.*


## Clone your Azure DevOps  to your computer

Once you've got an Azure DevOps (ADO) project, click "Repos" to get started. You may follow the instructions at [docs.microsoft.com/](https://docs.microsoft.com/en-us/azure/devops/repos/git/clone?tabs=visual-studio&view=azure-devops#clone-a-repo) to see the many ways you can clone it to your computer. You can't clone a repo without a clone URL. 

### **Clone to your computer...**
If you are using the Command Line, pass this clone URL to ```git clone``` to make a local copy of the repo:

```git clone https://<org>@dev.azure.com/<org>/<proj>/_git/<proj>```

```git clone``` clones the repository from the URL in a folder under the current one.

### **...or push an existing repository from command line...**
If you are taking an existing Unity project (that must have a proper .gitignore in it), you may wish to [push it to Azure DevOps](https://docs.microsoft.com/en-us/vsts/git/tutorial/pushing?tabs=visual-studio):

``` git remote add origin https://<org>@dev.azure.com/<org>/<proj>/_git/<proj> ```

```git push -u origin --all ```

### **...or import a repository...**
There is an "Import" button that allows you to import another Git repository from a source control website, such as GitHub or ADO. Choose this if you already have a project repository elsewhere that you want to reference. Please ensure that it has a Unity-specific gitignore file in it.

### **...or initialize with a ReadMe or .gitignore**
You may choose to take this shortcut to initialize this repository with a .gitignore file and an optional ReadMe empty file. Note that once you click the "Initialize" button, your repo is no longer considered empty, and will now navigate you to the Files. 

Azure DevOps (and many other services) pull from GitHub's official ```gitignore``` list, if you wish to choose the [Unity.gitignore](https://github.com/github/gitignore/blob/master/Unity.gitignore). 

## Introduce a Unity Project
You will eventually need to tell an Azure Pipeline where to get source code for your Unity project. Please ensure that you have added a Unity-specific gitignore file to your ADO repo, and that you haven't already committed any files that need to be ignored during the cloning process. Your Unity project's Asset folder should become the ADO repository root. 

It is encouraged to stay up-to-date with the lastest software versions of Unity and Visual Studio, so we are going to proceed with **Unity 2019** *(2019.2.9f1 at the time of this writing)* and **Visual Studio 2019** *(16.3.6 at the time of this writing)* moving forward. 

*Older versions of Unity 2018 and Visual Studio 2017 are  supported, with some customization to the pipeline.*

Since we are adding tools to build & deploy Unity 3D projects designed for Windows 10 UWP apps, please configure the Unity project you want to work with to properly support Windows Mixed Reality,  Virtual Reality, spatial audio, XR settings, etc. by following the instructions here:
[How To Configure a New Unity Project for Windows Mixed Reality.](https://docs.microsoft.com/en-us/windows/mixed-reality/configure-unity-project) **The MRTK "Foundation" Unity Package must be imported into your Unity project, but the rest of it may be out-of-the-box empty and named anything.**

Additional Resources:

- [Windows Mixed Reality Development and Design Documentation](https://developer.microsoft.com/windows/mixed-reality)

- [Windows Mixed Reality Academy](https://docs.microsoft.com/en-us/windows/mixed-reality/tutorials)

Be sure to quit Unity to prepare for commiting to a git repository. If you have a proper .gitignore and haven't commited any files you don't need, then it should automatically ignore the directories such as Library, Temp, and Obj, the .csproj's, and almost everything else except the Assets and ProjectSettings directories. 

Once you add, commit, and push your Unity project files up to ADO, the only things you see in your repo's files should be Assets, Packages, ProjectSettings, and .gitignore.

## ♨ Get our ✨**Azure-Pipelines.yml**✨ Homemade Recipe! ♨
We have already done the hard work for you! We are providing you with an [Azure Pipelines YAML file]() that will build your Unity project on a Windows Hosted Agent -- at no additional cost! 😉 

This build pipeline is designed for ARM UWP projects (what the HoloLens 2 uses), running Unity 2019.x, with MRTK Foundation. It is customizable to suit older versions of Unity, Visual Studio, and HoloLens 1. 

You do not need to use an empty build pipeline template to start from scratch, or  select which agent pools to use. 😎

### Download ```Azure-Pipelines.yml``` from...
- [Tobiah Zarlez's GitHub](https://github.com/TobiahZ/UnityAzurePipeline/blob/master/azure-pipelines.yml) (short term)
- the bottom of this [ReadMe.md](#the-yaml)
- CSE Eng Playbook? (long term)
- MS Flex? (deprecated?)

## Upload ```Azure-Pipelines.yml``` to your ADO Repo!
The easiest way to upload a file to ADO is to click the "Upload file(s)" button from your Repos page. Then you can simply drag and drop or browse for our ```azure-pipelines.yml``` file.

![Upload](/Images/upload.gif)

## Add your Secret Variables

This pipeline depends on Secret Variables that you must define within your Azure DevOps:
- [x] ```unity.username```  = Your Unity account email address
- [x] ```unity.password```  = Your Unity account password (keep it secret)
- [x] ```unity.serialkey``` = The serial key for your Unity Pro licence (keep it safe)

Without all three, this pipeline will fail to run. 🚫

![Secrets](/Images/secrets.gif)

Unity Pro will be licensed, utilized, and then unlicensed during the Unity and UnityTests jobs in this pipeline. A Unity Pro license works on 2 machines, and this pipeline should only use one licence at a time. However, should there be unexpected errors and deactivation doesn't complete properly, you may need to log into your Unity account and deactivate all. https://store.unity3d.com/account/licenses/other

## Create a new build pipeline
There are two ways to accomplish creating a new build pipeline in your ADO. In your Azure DevOps project, you may navigate to Pipelines by clicking "Set up build" (from the repo's files page) > "Existing Azure Pipelines YAML file."

![Build](/Images/build.gif)

Alternatively, click the "Pipelines" blue rocket icon > "New pipeline" button > "Azure Repos Git". 

![Pipeline](/Images/pipeline.gif)

Complete this step according to your needs.

# The YAML
Read the comments below for more delicious information on how this pipeline works.

```yml
# This build pipeline is designed for ARM UWP projects, running Unity 2019.x or later, with MRTK Foundation 
# If using 2018.x or earlier: Change unity.installComponents to 'Windows, UWP_IL2CPP'
# If not using MRTK Foundation: Change unity.executeMethod to your build method (We recommend basing off the one in MRTK Foundation)
# If you want to build x86 UWP builds: In theory should just be able to change vs.appxPlatforms, but it's a TODO

# This pipeline depends on secret variables that you must define within your Azure DevOps!
#   unity.username  = <Your Unity account username / email>
#   unity.password  = <Your Unity account password>
#   unity.serialkey = <The serial key for your Unity pro licence>
# Without all three, this pipeline will fail to run!

# Unity will be licensed, used, and then unlicensed during the unity and unitytests jobs in this pipeline.
# A Pro license works on 2 machines, and in theory this pipeline will only use one licence at a time.
# However, should there be unexpected errors and deactivation doesn't complete properly, you may need to log into your Unity account and deactivate all.

# Variables predefined for this build process:
variables:

  # By default, run tests. If you do not have tests, set to false to increase build speed.
  runTests: false

  # The path to the folder which contains the Assets folder of the project.
  # If your Unity project is located in a subfolder of your repo, make sure it is reflected in this.
  unity.projectPath:        '$(System.DefaultWorkingDirectory)/'

  # If you are using Unity 2019 or later, leave this alone!
  unity.installComponents:  'Windows, UWP'
  # If you are using Unity 2018 or earlier, comment out the above and uncomment below:
  #  unity.installComponents:  'Windows, UWP_IL2CPP'
  # Explanation: In Unity 2019 and later, .NET scripting was removed. You no longer need to specify 'UWP_IL2CPP', it's now simply 'UWP'!

  # The build method of the Unity project. This assumes you have MRTK in your project, and uses its build script.
  # If you want to customize your build script, change the method name here:
  unity.executeMethod:      'Microsoft.MixedReality.Toolkit.Build.Editor.UnityPlayerBuildTools.StartCommandLineBuild'

  # Are we buolding an .appx for x86 or ARM?
  # TODO: Theoretically, could expect to use 'x86|ARM' to build for both HL1 and 2, but yet to get that to work.
  vs.appxPlatforms:         'ARM'

  # I would not expect you to have to change the rest of these unless you had a special reason:
  unity.targetBuild:        'WindowsStoreApps'
  unity.outputPath:         '/Builds/WSAPlayer'
  unity.editorPath:         '/Editor/Unity.exe'
  vs.packagePath:           '/AppPackages'

  # This also needs to be passed to the install template, along with unity.projectPath
  unity.installFolder:      'C:/Program Files/Unity/Hub/Editor/'
  
# What causes us to build? A push to master or a feature branch causes us to build...
trigger:
  batch: true
  branches:
    include:
    - master
    - feature/*

# Windows machine with Visual Studio 2019:
pool:
  vmImage: 'windows-2019'


# Two jobs in this pipeline:
# - Build the Unity
# - Run Unity tests
# Note: The build job can be uncommented and broken up into two jobs, for when that makes sense.
jobs:

# Install Unity (from cache or download) then create Visual Studio project from Unity
- job: unity
  displayName: Unity Build
  variables:
    installCached: false
  # Try to ensure that we have the right secrets set up to continue, otherwise fail the job:
  condition: or( not(variables['unity.username']), not(variables['unity.password']), not(variables['unity.serialKey']) )
  steps:
  # What version of Unity does the project say that it wants?:
  - task: UnityGetProjectVersionTask@1
    name: unitygetprojectversion
    displayName: Calling UnityGetProjectVersionV1 from unity-azure-pipelines-tasks extension
    inputs:
      unityProjectPath: '$(unity.projectPath)'

# TODO: This is the start of code that is repeated in other jobs, and ought to be done via a seperate file template.
  # Do we have that Unity installation cached? If so, install from cache:
  # (Note: The key is the hashed contents of the ProjectVersion.txt file)
  # What is this? See https://docs.microsoft.com/en-us/azure/devops/pipelines/caching/index?view=azure-devops
  - task: CacheBeta@0
    displayName: Check if Unity installation is cached
    inputs:
      key: $(Agent.OS) | "$(unitygetprojectversion.projectVersion)" | "$(unity.installComponents)"
      path: "$(unity.installFolder)$(unitygetprojectversion.projectVersion)"
      cacheHitVar: installCached

  # Install the Unity setup module (if we aren't cached):
  - task: PowerShell@2  
    displayName: Install Unity
    condition: and(succeeded(), ne(variables['installCached'], true))
    inputs:
      targetType: 'inline'
      script: |
        Install-Module -Name UnitySetup -AllowPrerelease -Force -AcceptLicense

  # Download and run the installer for Unity Components defined in unity.installComponents:
  - task: PowerShell@2
    displayName: Installing Unity Components '$(unity.installComponents)'
    condition: and(succeeded(), ne(variables['installCached'], true))
    inputs:
      targetType: 'inline'
      script: |   
        Install-UnitySetupInstance -Installers (Find-UnitySetupInstaller -Version '$(unitygetprojectversion.projectVersion)' -Components $(unity.installComponents)) -Verbose
# TODO: This is the end of code that is repeated in other jobs, and ought to be done via a seperate file template.

  # Activate the Unity license (In theory, should deactivate the licence after use!):
  - task: UnityActivateLicenseTask@1
    displayName: Calling UnityActivateLicenseTask@1 from unity-azure-pipelines-tasks extension
    inputs:
      username: '$(unity.username)'
      password: '$(unity.password)'
      serial: '$(unity.serialkey)'
      unityEditorsPathMode: 'unityHub'
      unityProjectPath: '$(unity.projectPath)'

  # Build the project with Unity using the script defined in unity.executeMethod:
  - task: UnityBuildTask@3
    displayName: Calling UnityBuildTask@3 from unity-azure-pipelines-tasks extension
    name: runbuild
    inputs:
      buildScriptType: existing
      scriptExecuteMethod: '$(unity.executeMethod)'
      buildTarget: '$(unity.targetBuild)'
      unityProjectPath: '$(unity.projectPath)'
      outputPath: '$(Build.BinariesDirectory)'

  # Publish the Solution folder:
  - task: PublishPipelineArtifact@0
    displayName: 'Publish Pipeline Artifact'
    inputs:
      artifactName: 'sln'
      targetPath: '$(unity.projectPath)$(unity.outputPath)'


# Until we have a Unity serial key management system, the unity and slnbuild jobs can be merged for speed.
# # Build the Visual Studio solution generated from the previous job and create a package.
# - job: slnbuild
#   displayName: Build Visual Studio project
#   dependsOn: unity
#   condition: succeeded()
#   variables:
#     installCached: false
#     unityversion: $[ dependencies.unity.outputs['unitygetprojectversion.projectVersion'] ]
#   steps:
#   # Skip checking out repository
#   - checkout: none 

#   # Download artifacts from last job
#   - task: DownloadPipelineArtifact@2
#     displayName: 'Download Pipeline Artifacts'
#     inputs:
#       artifactName: 'sln'
#       targetPath: $(Build.SourcesDirectory)

# # TODO: This is the start of code that is repeated from the unity job, and ought to be done via a seperate file template.
#   # Do we have that Unity installation cached? If so, install from cache:
#   # (Note: The key is the hashed contents of the ProjectVersion.txt file)
#   # What is this? See https://docs.microsoft.com/en-us/azure/devops/pipelines/caching/index?view=azure-devops
#   - task: CacheBeta@0
#     displayName: Check if Unity installation is cached
#     inputs:
#       key: $(Agent.OS) | "$(unityversion)" | "$(unity.installComponents)"
#       path: "$(unity.installFolder)$(unityversion)"
#       cacheHitVar: installCached

#   # Install the Unity setup module (if we aren't cached):
#   - task: PowerShell@2  
#     displayName: Install Unity
#     condition: and(succeeded(), ne(variables['installCached'], true))
#     inputs:
#       targetType: 'inline'
#       script: |
#         Install-Module -Name UnitySetup -AllowPrerelease -Force -AcceptLicense

#   # Download and run the installer for Unity Components defined in unity.installComponents:
#   - task: PowerShell@2
#     displayName: Installing Unity Components '$(unity.installComponents)'
#     condition: and(succeeded(), ne(variables['installCached'], true))
#     inputs:
#       targetType: 'inline'
#       script: |   
#         Install-UnitySetupInstance -Installers (Find-UnitySetupInstaller -Version '$(unityversion)' -Components $(unity.installComponents)) -Verbose
# # TODO: This is the end of code that is repeated from the unity job, and ought to be done via a seperate file template.
# Uncomment the above, and change '$(unity.projectPath)$(unity.outputPath)' below to '$(Build.SourcesDirectory)' to resume two seperate jobs

  # Find, download, and cache NuGet:
  - task: NuGetToolInstaller@1
    displayName: 'Install NuGet'

  # Restore the NuGet packages for the solution:
  - task: NuGetCommand@2
    displayName: 'NuGet restore'
    inputs:
      restoreSolution: '$(unity.projectPath)$(unity.outputPath)/*.sln' # Change to '$(unity.projectPath)$(unity.outputPath)/*.sln' to resume two jobs

  # Build the solution with Visual Studio to make an .appx:
  - task: MSBuild@1
    displayName: 'Build solution'
    inputs:
      solution: '$(unity.projectPath)$(unity.outputPath)' # Change to '$(unity.projectPath)$(unity.outputPath)' to resume two jobs
      configuration: Release
      msbuildArguments: '/p:AppxBundle=Always /p:AppxBundlePlatforms="$(vs.appxPlatforms)"'

  # Publish the package (.appxbundle/.msixbundle) that we just built:
  - task: PublishPipelineArtifact@0
    displayName: 'Publish Pipeline Artifact'
    inputs:
      artifactName: 'apppackages'
      targetPath: '$(unity.projectPath)$(unity.outputPath)$(vs.packagePath)'  # Change to '$(unity.projectPath)$(unity.outputPath)$(vs.packagePath)' to resume two jobs


# Build the Visual Studio solution generated from the previous job and create a package.
- job: unitytests
  dependsOn: unity
  condition: and(succeeded(), eq(variables['runTests'], 'true'))
  displayName: Unity Tests
  variables:
    installCached: false
  steps:
  # What version of Unity does the project say that it wants?:
  - task: UnityGetProjectVersionTask@1
    name: unitygetprojectversion
    displayName: Calling UnityGetProjectVersionV1 from unity-azure-pipelines-tasks extension
    inputs:
      unityProjectPath: '$(unity.projectPath)'

# TODO: This is the start of code that is repeated from the unity job, and ought to be done via a seperate file template.
  # Do we have that Unity installation cached? If so, install from cache:
  # (Note: The key is the hashed contents of the ProjectVersion.txt file)
  # What is this? See https://docs.microsoft.com/en-us/azure/devops/pipelines/caching/index?view=azure-devops
  - task: CacheBeta@0
    displayName: Check if Unity installation is cached
    inputs:
      key: $(Agent.OS) | "$(unitygetprojectversion.projectVersion)" | "$(unity.installComponents)"
      path: "$(unity.installFolder)$(unitygetprojectversion.projectVersion)"
      cacheHitVar: installCached

  # Install the Unity setup module (if we aren't cached):
  - task: PowerShell@2  
    displayName: Install Unity
    condition: and(succeeded(), ne(variables['installCached'], true))
    inputs:
      targetType: 'inline'
      script: |
        Install-Module -Name UnitySetup -AllowPrerelease -Force -AcceptLicense

  # Download and run the installer for Unity Components defined in unity.installComponents:
  - task: PowerShell@2
    displayName: Installing Unity Components '$(unity.installComponents)'
    condition: and(succeeded(), ne(variables['installCached'], true))
    inputs:
      targetType: 'inline'
      script: |   
        Install-UnitySetupInstance -Installers (Find-UnitySetupInstaller -Version '$(unitygetprojectversion.projectVersion)' -Components $(unity.installComponents)) -Verbose
# TODO: This is the end of code that is repeated from the unity job, and ought to be done via a seperate file template.

  # Activate the Unity license (In theory, should deactivate the licence after use!):
  - task: UnityActivateLicenseTask@1
    displayName: Calling UnityActivateLicenseTask@1 from unity-azure-pipelines-tasks extension
    inputs:
      username: '$(unity.username)'
      password: '$(unity.password)'
      serial: '$(unity.serialkey)'
      unityEditorsPathMode: 'unityHub'
      unityProjectPath: '$(unity.projectPath)'

  # Test scripts copied from the MRTK pipeline test script, located here:
  # https://github.com/microsoft/MixedRealityToolkit-Unity/blob/mrtk_release/pipelines/templates/tests.yml
  # NOTE: If you do not have Unit Tests in your project, you can speed up build times by changing runTests to false.

  # Edit mode tests:
  # By default, this is left commented out. Can uncomment if you have edit mode tests in your project.
  # - powershell: |   
  #     Write-Host "======================= EditMode Tests ======================="

  #     $logFile = New-Item -Path .\editmode-test-run.log -ItemType File -Force

  #     $proc = Start-Process -FilePath "$(unity.installFolder)$(unitygetprojectversion.projectVersion)$(unity.editorPath)" -ArgumentList "-projectPath $(unity.projectPath) -runTests -testPlatform editmode -batchmode -logFile $($logFile.Name) -editorTestsResultFile .\test-editmode-default.xml" -PassThru
  #     $ljob = Start-Job -ScriptBlock { param($log) Get-Content "$log" -Wait } -ArgumentList $logFile.FullName

  #     while (-not $proc.HasExited -and $ljob.HasMoreData)
  #     {
  #         Receive-Job $ljob
  #         Start-Sleep -Milliseconds 200
  #     }
  #     Receive-Job $ljob

  #     Stop-Job $ljob

  #     Remove-Job $ljob
  #     Stop-Process $proc
  #   displayName: 'Run EditMode tests'

  # Play mode tests:
  - powershell: |
      Write-Host "======================= PlayMode Tests ======================="

      $logFile = New-Item -Path .\playmode-test-run.log -ItemType File -Force

      $proc = Start-Process -FilePath "$(unity.installFolder)$(unitygetprojectversion.projectVersion)$(unity.editorPath)" -ArgumentList "-projectPath $(unity.projectPath) -runTests -testPlatform playmode -batchmode -logFile $($logFile.Name) -editorTestsResultFile .\test-playmode-default.xml" -PassThru
      $ljob = Start-Job -ScriptBlock { param($log) Get-Content "$log" -Wait } -ArgumentList $logFile.FullName

      while (-not $proc.HasExited -and $ljob.HasMoreData)
      {
          Receive-Job $ljob
          Start-Sleep -Milliseconds 200
      }
      Receive-Job $ljob

      Stop-Job $ljob

      Remove-Job $ljob
      Stop-Process $proc
    displayName: 'Run PlayMode tests'

  # Publish test results:
  - task: PublishTestResults@2
    displayName: 'Publish Test Results'
    inputs:
      testResultsFormat: NUnit
      testResultsFiles: 'test*.xml'
      failTaskOnFailedTests: true