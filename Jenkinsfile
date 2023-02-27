pipeline {
  agent any
  stages {
    stage('ERP_A_TrunkCheckout') {
      steps {
        svn checkout([$class: 'SubversionSCM', additionalCredentials: [], excludedCommitMessages: '', excludedRegions: '', excludedRevprop: '', excludedUsers: '', filterChangelog: false, ignoreDirPropChanges: false, includedRegions: '', locations: [[cancelProcessOnExternalsFail: true, credentialsId: '7ef78ec7-cf02-4873-b585-3be09937c0e9', depthOption: 'infinity', ignoreExternalsOption: true, local: '.', remote: 'https://alliance-vm03/svn/ERP_ALLIANCE_ARMAND/trunk']], quietOperation: true, workspaceUpdater: [$class: 'UpdateUpdater']])
      }
    }

    stage('ParallelStage_1') {
      environment {
        DirToPurge = 'C:\\Livrables\\All_dotnet'
      }
      parallel {
        stage('ERP_B-1_RestoreNuget') {
          steps {
            bat '"%WORKSPACE%\\build\\nuget.exe" restore "%WORKSPACE%\\GCRADC.sln"'
          }
        }

        stage('ERP_B-2_PurgeLivrables') {
          steps {
            script {
              try {
                powershell '''
$DirectoryToPurge="$($env:DirToPurge)"
#$DirectoryToPurge="C:\\Livrables\\All_dotnet"
$count=0
#Creer le repertoire de base du livrable s\\\'il n\\\'existe pas
if ( Test-Path $($DirectoryToPurge) ) {
$getAllFilesLivrableDirectory=gci $DirectoryToPurge -File -Recurse
$getAllFilesLivrableDirectory |%{
Remove-Item $($_.Fullname) -Recurse -Force
"Fichier \'$($_.Fullname)\' supprimé"
$count++
}
"---"
"$($count) fichiers purgés dans \'$($DirectoryToPurge)\'"
"---"
}
'''
                println "Purge \'$DirToPurge\' success!!"
              } catch (err){
                println "Purge \'$DirToPurge\' failed: ${err}!!"
              }
            }

          }
        }

      }
    }

    stage('ParallelStage_2') {
      parallel {
        stage('ERP_C-1_BuildSolution') {
          steps {
            script {
              try {
                // Build solution step
                powershell 'C:\\\'Program Files (x86)\'\\\'Microsoft Visual Studio\'\\2019\\Professional\\MSBuild\\Current\\Bin\\MSBuild.exe .\\GCRADC.sln /p:Configuration=Release'
                println "Build GCRADC.sln successfull!!"
              } catch (err){
                println "Build GCRADC.sln failed: ${err}"
              }
            }

          }
        }

        stage('ERP_C-2_CopyWorkspaceLivrables') {
          environment {
            SourceDir = 'C:\\Jenkins\\JenkinsHome\\workspace\\ERP_main'
            DestinationDir = 'C:\\Livrables'
            BaseOutputDirectory = 'All_dotnet'
          }
          steps {
            script {
              try {
                // Get some code from a svn trunk repository
                powershell '''
$SourceDirectory="C:\\Jenkins\\JenkinsHome\\workspace\\ERP_main"
#$SourceDirectory = "\\\\aci-cicd\\Livrables\\All_dotnet\\Tests.*"
#$SourceDirectory="$($env:SourceDir)"
$DestinationDirectory = "\\\\ALLIANCE-VM03\\c$\\Livrables"
#$DestinationDirectory="$($env:DestinationDir)"
$DestinationDirectoryName = "All_dotnet"
#$DestinationDirectoryName="$($env:BaseOutputDirectory)"
$count=0
#Creer le repertoire de base du livrable s\'il n\'existe pas
if ( -not (Test-Path "$($DestinationDirectory)\\$($DestinationDirectoryName)") -and ($($DestinationDirectoryName) -ne "") ) {
New-Item -ItemType Directory "$($DestinationDirectory)\\$($DestinationDirectoryName)"
"`nLe repertoire \'$($DestinationDirectoryName)\' specifie inexistant a ete créé dans \'$($DestinationDirectory)\'`n"
"-------------------------"
}
if ( (Test-Path $($SourceDirectory)) -and (Test-Path $($DestinationDirectory)) ) {
$DestinationDirectory="$($DestinationDirectory)\\$($DestinationDirectoryName)"
$SourceDirectory |%{
$SourceDirectoryDirs=gci  $($SourceDirectory) -Directory
$SourceDirectoryFiles=gci $($SourceDirectory) -File
#Copy directories from SourceDirectory to DestinationDirectory
$SourceDirectoryDirs |%{
$item=$_
if (Test-Path $($SourceDirectory) -PathType Container) {
Copy-Item -Path "$($item.Fullname)" -Destination "$($DestinationDirectory)" -Recurse -Force
"Repertoire \'$($item.Name)\' copié"
$count++
}
}
"-------------------------"
#Delete .svn directory
$GetAllDestDirectory=gci  $($DestinationDirectory) -Directory -Recurse
$GetAllDestDirectory |%{
if ( (Test-Path $_.FullName -PathType Container) -and ($_.BaseName -eq \'.svn\') ) {
Remove-Item $($_.Fullname) -Recurse -Force
"Repertoire \'$($_.Fullname)\' supprimé"
}
}
"-------------------------"
#Copy files from SourceDirectory to DestinationDirectory
$SourceDirectoryFiles |%{
$item=$_
Copy-Item "$($item.Fullname)" -Destination "$($DestinationDirectory)" -Force
"Fichier \'$($item.Name)\' copié"
$count++
}
}
"---"
"$($count) items copiés dans \'$($DestinationDirectory)\'"
"---"
} else {
"Erreur!!! Verifier l\'existence des repertoires source et destination!!"
}
'''
                println "Copy \'$SourceDir\' to \'$DestinationDir\\$BaseOutputDirectory\' success!!"
              } catch (err) {
                println "Copy \'$SourceDir\' to \'$DestinationDir\\$BaseOutputDirectory\' failed: ${err}!!"
              }
            }

          }
        }

        stage('ERP_C-3_CopyDLLsLivrables') {
          environment {
            SourceDir = '\\\\aci-cicd\\Livrables\\All_dotnet\\Tests.*'
            DestinationDir = '\\\\ALLIANCE-VM03\\c$\\Livrables'
            BaseOutputDirectory = 'All_dotnet'
          }
          steps {
            script {
              try {
                // Get some code from a svn trunk repository
                powershell '''
#$SourceDirectory="C:\\Jenkins\\JenkinsHome\\workspace\\ERP_main"
#$SourceDirectory = "\\\\aci-cicd\\Livrables\\All_dotnet\\Tests.*"
$SourceDirectory="$($env:SourceDir)"
#$DestinationDirectory = "\\\\ALLIANCE-VM03\\c$\\Livrables"
$DestinationDirectory="$($env:DestinationDir)"
#$DestinationDirectoryName = "All_dotnet"
$DestinationDirectoryName="$($env:BaseOutputDirectory)"
$count=0
#Creer le repertoire de base du livrable s\'il n\'existe pas
if ( -not (Test-Path "$($DestinationDirectory)\\$($DestinationDirectoryName)") -and ($($DestinationDirectoryName) -ne "") ) {
New-Item -ItemType Directory "$($DestinationDirectory)\\$($DestinationDirectoryName)"
"`nLe repertoire \'$($DestinationDirectoryName)\' specifie inexistant a ete créé dans \'$($DestinationDirectory)\'`n"
"-------------------------"
}
if ( (Test-Path $($SourceDirectory)) -and (Test-Path $($DestinationDirectory)) ) {
$DestinationDirectory="$($DestinationDirectory)\\$($DestinationDirectoryName)"
$SourceDirectory |%{
$SourceDirectoryDirs=gci  $($SourceDirectory) -Directory
$SourceDirectoryFiles=gci $($SourceDirectory) -File
#Copy directories from SourceDirectory to DestinationDirectory
$SourceDirectoryDirs |%{
$item=$_
if (Test-Path $($SourceDirectory) -PathType Container) {
Copy-Item -Path "$($item.Fullname)" -Destination "$($DestinationDirectory)" -Recurse -Force
"Repertoire \'$($item.Name)\' copié"
$count++
}
}
"-------------------------"
#Delete .svn directory
$GetAllDestDirectory=gci  $($DestinationDirectory) -Directory -Recurse
$GetAllDestDirectory |%{
if ( (Test-Path $_.FullName -PathType Container) -and ($_.BaseName -eq \'.svn\') ) {
Remove-Item $($_.Fullname) -Recurse -Force
"Repertoire \'$($_.Fullname)\' supprimé"
}
}
"-------------------------"
#Copy files from SourceDirectory to DestinationDirectory
$SourceDirectoryFiles |%{
$item=$_
Copy-Item "$($item.Fullname)" -Destination "$($DestinationDirectory)" -Force
"Fichier \'$($item.Name)\' copié"
$count++
}
}
"---"
"$($count) items copiés dans \'$($DestinationDirectory)\'"
"---"
} else {
"Erreur!!! Verifier l\'existence des repertoires source et destination!!"
}
'''
                println "Copy \'$SourceDir\' to \'$DestinationDir\\$BaseOutputDirectory\' success!!"
              } catch (err) {
                println "Copy \'$SourceDir\' to \'$DestinationDir\\$BaseOutputDirectory\' failed: ${err}!!"
              }
            }

          }
        }

      }
    }

    stage('ERP_D_LaunchDLLs') {
      environment {
        BaseOutputRootDir = 'C:\\Livrables'
        BaseOutputDir = 'All_dotnet'
      }
      steps {
        script {
          try {
            powershell '''$vstestDir="C:\\Program Files (x86)\\Microsoft Visual Studio\\2019\\Professional\\Common7\\IDE\\Extensions\\TestPlatform"
$listeDLLs=\'Common.DaosTests.dll\',\'Common.ServicesTests.dll\',\'CommonTests.dll\'
#$BaseOutputRootDirectory="C:\\Livrables"
$BaseOutputRootDirectory="$($env:BaseOutputRootDir)"
#$BaseOutputDirectory="All_dotnet"
$BaseOutputDirectory="$($env:BaseOutputDir)"
foreach ($it in $listeDLLs) {
Try {
if ( $it -eq "Common.DaosTests.dll" ) {
$rep="Tests.Daos"
} elseif ( $it -eq "Common.ServicesTests.dll" ) {
$rep="Tests.Services"
} elseif ( $it -eq "CommonTests.dll" ) {
$rep="Tests.Commun"
}
. "$($vstestDir)\\vstest.console.exe" "$($BaseOutputRootDirectory)\\$($BaseOutputDirectory)\\$($rep)\\$($it)"
"`nExecution terminée sans erreur pour \'$($rep)\\$($it)\' !!`n"
"-------------------------`n"
} catch {
"`nErreur lors de l\'execution de \'$($rep)\\$($it)\' !!`n"
"An error occurred: $_"
"`n-------------------------`n"
}
}'''
            println "Launch DLLs in \'$BaseOutputRootDir\\$BaseOutputDir\' success!!"
          } catch (err){
            println "Launch DLLs in \'$BaseOutputRootDir\\$BaseOutputDir\' failed: ${err}!!"
          }
        }

      }
    }

    stage('ERP_E_InstallNpm') {
      environment {
        BaseOutputRootDir = 'C:\\Jenkins\\JenkinsHome\\workspace\\ERP_main'
        GenererAngular = 'Oui'
      }
      steps {
        script {
          powershell '''# --- DEBUT PORTAGE -------------------------------------------------------------------------------------------------

#$BaseOutputRootDirectory="C:\\Jenkins\\JenkinsHome\\workspace\\ERP_Pipeline_master"
$BaseOutputRootDirectory="C:\\Jenkins\\JenkinsHome\\workspace\\ERP_main"

$InstallInAngular_PortageOrNot = "Oui"
#$InstallInAngular_PortageOrNot="$($env:InstallInAngular_PortageOrNot)"
$InstallInAngular_TableauDeBordOrNot = "Oui"
#$InstallInAngular_TableauDeBordOrNot="$($env:InstallInAngular_TableauDeBordOrNot)"

$listeDirs=\'Portage.Angular\\app\',\'TableauDeBord.Angular\\tableauDeBord\'

function installNPM {

param (
$rep
)

Try {

Set-Location $($rep)
. npm install
"`nInstallation NPM in \'$($rep)\' success!!"
"`n-------------------------"

} catch {

"`nInstallation NPM in \'$($rep)\' failed: ${err}!!"
"`n-------------------------"
}

}


foreach ($it in $listeDirs) {

$repert = "$($BaseOutputRootDirectory)\\$($it)\\src"

if ( ($InstallInAngular_PortageOrNot -eq "Oui") -and (Test-Path -Path $repert) ) {

installNPM -rep $repert

} elseif ( ($InstallInAngular_TableauDeBordOrNot -eq "Oui") -and (Test-Path -Path $repert) ) {

installNPM -rep $repert

}

}'''
        }

      }
    }

    stage('ERP_F_RunBuildNPM') {
      environment {
        InstallInAngular_PortageOrNot = 'Oui'
        InstallInAngular_TableauDeBordOrNot = 'Oui'
      }
      steps {
        script {
          powershell '''# --- DEBUT PORTAGE -------------------------------------------------------------------------------------------------

#$BaseOutputRootDirectory="C:\\Jenkins\\JenkinsHome\\workspace\\ERP_Pipeline_master"
$BaseOutputRootDirectory="C:\\Jenkins\\JenkinsHome\\workspace\\ERP_main"

$InstallInAngular_PortageOrNot = "Oui"
#$InstallInAngular_PortageOrNot="$($env:InstallInAngular_PortageOrNot)"
$InstallInAngular_TableauDeBordOrNot = "Oui"
#$InstallInAngular_TableauDeBordOrNot="$($env:InstallInAngular_TableauDeBordOrNot)"

$listeDirs=\'Portage.Angular\\app\',\'TableauDeBord.Angular\\tableauDeBord\'

function RunNPM {

param (
$rep
)

Try {

Set-Location $($rep)
. npm run build -- --configuration production
"`nRun Build in \'$($rep)\' success!!"
"`n-------------------------"

} catch {

"`nRun Build in \'$($rep)\' failed: ${err}!!"
"`n-------------------------"
}

}


foreach ($it in $listeDirs) {

$repert = "$($BaseOutputRootDirectory)\\$($it)\\src"

if ( ($InstallInAngular_PortageOrNot -eq "Oui") -and (Test-Path -Path $repert) ) {

RunNPM -rep $repert

} elseif ( ($InstallInAngular_TableauDeBordOrNot -eq "Oui") -and (Test-Path -Path $repert) ) {

RunNPM -rep $repert

}

}'''
        }

      }
    }

    stage('ERP_G_CopyFilesToSPA') {
      environment {
        SourceDir = 'C:\\Jenkins\\JenkinsHome\\workspace\\ERP_main'
        DestinationDir = 'C:\\Livrables\\All_dotnet'
        DestinationDirName1 = 'Portage.Web\\SPA'
        DestinationDirName2 = 'TableauDeBord.Web\\SPA'
        DestinationDirName3 = 'TableauDeBord.Web\\SPA\\assets\\Conges'
        DestinationDirName4 = 'TableauDeBord.Web\\SPA\\assets\\javascripts'
        DestinationDirName5 = 'TableauDeBord.Web\\SPA\\assets\\img'
        DestinationDirName6 = 'TableauDeBord.Web\\SPA\\assets'
      }
      steps {
        powershell '''
#$SourceDirectory="C:\\Jenkins\\JenkinsHome\\workspace\\ERP_main"
#$SourceDirectory = "\\\\aci-cicd\\Livrables\\All_dotnet\\Tests.*"
$SourceDirectory="$($env:SourceDir)"
#$DestinationDirectory = "C:\\Livrables\\All_dotnet"
$DestinationDirectory="$($env:DestinationDir)"
#$DestinationDirectoryName1 = "Portage.Web\\SPA"
$DestinationDirectoryName1="$($env:DestinationDirName1)"
#$DestinationDirectoryName2 = "TableauDeBord.Web\\SPA"
$DestinationDirectoryName2="$($env:DestinationDirName2)"
#$DestinationDirectoryName3 = "TableauDeBord.Web\\SPA\\assets\\Conges"
$DestinationDirectoryName3="$($env:DestinationDirName3)"
#$DestinationDirectoryName4 = "TableauDeBord.Web\\SPA\\assets\\javascripts"
$DestinationDirectoryName4="$($env:DestinationDirName4)"
#$DestinationDirectoryName5 = "TableauDeBord.Web\\SPA\\assets\\img"
$DestinationDirectoryName5="$($env:DestinationDirName5)"
#$DestinationDirectoryName6 = "TableauDeBord.Web\\SPA\\assets"
$DestinationDirectoryName6="$($env:DestinationDirName6)"

#Creation des arborescences
if ( -not (Test-Path "$($DestinationDirectory)\\$($DestinationDirectoryName1)") -and ($($DestinationDirectoryName1) -ne "") ) {
    
    New-Item -ItemType Directory "$($DestinationDirectory)\\$($DestinationDirectoryName1)"
    "`nLe repertoire \'$($DestinationDirectoryName1)\' inexistant a ete créé dans \'$($DestinationDirectory)\'`n"
    "-------------------------"
} 
if ( -not (Test-Path "$($DestinationDirectory)\\$($DestinationDirectoryName2)") -and ($($DestinationDirectoryName2) -ne "") ) {
    
    New-Item -ItemType Directory "$($DestinationDirectory)\\$($DestinationDirectoryName2)"
    "`nLe repertoire \'$($DestinationDirectoryName2)\' inexistant a ete créé dans \'$($DestinationDirectory)\'`n"
    "-------------------------"
}
if ( -not (Test-Path "$($DestinationDirectory)\\$($DestinationDirectoryName3)") -and ($($DestinationDirectoryName3) -ne "") ) {
    
    New-Item -ItemType Directory "$($DestinationDirectory)\\$($DestinationDirectoryName3)"
    "`nLe repertoire \'$($DestinationDirectoryName3)\' inexistant a ete créé dans \'$($DestinationDirectory)\'`n"
    "-------------------------"
}
if ( -not (Test-Path "$($DestinationDirectory)\\$($DestinationDirectoryName4)") -and ($($DestinationDirectoryName4) -ne "") ) {
    
    New-Item -ItemType Directory "$($DestinationDirectory)\\$($DestinationDirectoryName4)"
    "`nLe repertoire \'$($DestinationDirectoryName4)\' inexistant a ete créé dans \'$($DestinationDirectory)\'`n"
    "-------------------------"
}
if ( -not (Test-Path "$($DestinationDirectory)\\$($DestinationDirectoryName5)") -and ($($DestinationDirectoryName5) -ne "") ) {
    
    New-Item -ItemType Directory "$($DestinationDirectory)\\$($DestinationDirectoryName5)"
    "`nLe repertoire \'$($DestinationDirectoryName5)\' inexistant a ete créé dans \'$($DestinationDirectory)\'`n"
    "-------------------------"
}

if ( Test-Path $($SourceDirectory) ) {
        
    $DestinationDirectory1="$($DestinationDirectory)\\$($DestinationDirectoryName1)"
    
    #Copy In $DestinationDirectory1
    if ( Test-Path $($DestinationDirectory1) ) {
        $countCSS=0
        $source1="$($SourceDirectory)\\Portage.Angular\\app\\dist\\app"
                
        $SourceDirectoryFilesCSS11 = gci $source1 | Where-Object { $_.Extension -eq ".css" }
        "Copy .css file from \'$($source1)\'"
        $SourceDirectoryFilesCSS11 |%{
            "`tCopy of \'$($_.Name)\' to \'$($DestinationDirectory1)\'"
		    Copy-Item $_.FullName -Destination "$($DestinationDirectory1)" -Force
            $countCSS++
        }
        "---"
        $source2="$($SourceDirectory)\\TableauDeBord.Angular\\tableauDeBord\\dist\\tableau-de-bord"
        $SourceDirectoryFilesCSS12 = gci $source2 | Where-Object { $_.Extension -eq ".css" }
        "Copy .css file from \'$($source2)\'"
        $SourceDirectoryFilesCSS12 |%{
            "`tCopy of \'$($_.Name)\' to \'$($DestinationDirectory1)\'"
            Copy-Item $_.FullName -Destination "$($DestinationDirectory1)" -Force
            $countCSS++
        }
        "---"
        "$($countCSS) file(s) \'.css\' copied"
	    "---"
        "-------------------------"
        
        $countJS=0
        "Copy .js file from \'$($source1)\'"
        $SourceDirectoryFilesJS11 = gci $source1 | Where-Object { $_.Extension -eq ".js" }
        $SourceDirectoryFilesJS11 |%{
            "`tCopy of \'$($_.Name)\' to \'$($DestinationDirectory1)\'"
		    Copy-Item $_.FullName -Destination "$($DestinationDirectory1)" -Force
            $countJS++
        }
        "---"
        "$($countJS) file(s) \'.js\' copied"
	    "---"
    }

    $DestinationDirectory2="$($DestinationDirectory)\\$($DestinationDirectoryName2)"   
    #Copy In $DestinationDirectory2
    if ( Test-Path $($DestinationDirectory2) ) {
        "-------------------------`n"
        $countJS=0
        $source="$($SourceDirectory)\\TableauDeBord.Angular\\tableauDeBord\\dist\\tableau-de-bord"
        "Copy .js file from \'$($source)\'"

        $SourceDirectoryFilesJS=gci $source | Where-Object { $_.Extension -eq ".js" }
        $SourceDirectoryFilesJS |%{
            "`tCopy of \'$($_.Name)\' to \'$($DestinationDirectory2)\'"
		    Copy-Item $_.FullName -Destination "$($DestinationDirectory2)" -Force
            $countJS++
        }
        "---"
        "$($countJS) file(s) \'.js\' copied"
	    "---"
    }

    $DestinationDirectory3="$($DestinationDirectory)\\$($DestinationDirectoryName3)"
    #Copy In $DestinationDirectory3
    if ( Test-Path $($DestinationDirectory3) ) {
        "-------------------------`n"
        $countJS=0
        $source="$($SourceDirectory)\\TableauDeBord.Angular\\tableauDeBord\\dist\\tableau-de-bord\\assets\\Conges"
        "Copy .js file from \'$($source)\'"

        $SourceDirectoryFilesJS=gci $source | Where-Object { $_.Extension -eq ".js" }
        $SourceDirectoryFilesJS |%{
            "`tCopy of \'$($_.Name)\' to \'$($DestinationDirectory3)\'"
		    Copy-Item $_.FullName -Destination "$($DestinationDirectory3)" -Force
            $countJS++
        }
        "---"
        "$($countJS) file(s) \'.js\' copied"
	    "---"
    }

    $DestinationDirectory4="$($DestinationDirectory)\\$($DestinationDirectoryName4)"
    #Copy In $DestinationDirectory4
    if ( Test-Path $($DestinationDirectory4) ) {
        "-------------------------`n"
        $countJS=0
        $source="$($SourceDirectory)\\TableauDeBord.Angular\\tableauDeBord\\dist\\tableau-de-bord\\assets\\javascripts"
        "Copy .js file from \'$($source)\'"

        $SourceDirectoryFilesJS=gci $source | Where-Object { $_.Extension -eq ".js" }
        $SourceDirectoryFilesJS |%{
            "`tCopy of \'$($_.Name)\' to \'$($DestinationDirectory4)\'"
		    Copy-Item $_.FullName -Destination "$($DestinationDirectory4)" -Force
            $countJS++
        }
        "---"
        "$($countJS) file(s) \'.js\' copied"
	    "---"
    }

    $DestinationDirectory5="$($DestinationDirectory)\\$($DestinationDirectoryName5)"
    #Copy In $DestinationDirectory5
    if ( Test-Path $($DestinationDirectory5) ) {
        "-------------------------`n"
        $countPNG=0
        $source="$($SourceDirectory)\\Portage.Angular\\app\\dist\\app\\assets\\img"
        "Copy .png file from \'$($source)\'"

        $SourceDirectoryFilesPNG=gci $source | Where-Object { $_.Extension -eq ".png" }
        $SourceDirectoryFilesPNG |%{
            "`tCopy of \'$($_.Name)\' to \'$($DestinationDirectory5)\'"
		    Copy-Item $_.FullName -Destination "$($DestinationDirectory5)" -Force
            $countPNG++
        }
        "---"
        "$($countPNG) file(s) \'.png\' copied"
	    "---"
    }

    $DestinationDirectory6="$($DestinationDirectory)\\$($DestinationDirectoryName6)"
    #Copy In $DestinationDirectory6
    if ( Test-Path $($DestinationDirectory6) ) {    
        "-------------------------`n"
        $countPNG=0
        $source="$($SourceDirectory)\\TableauDeBord.Angular\\tableauDeBord\\dist\\tableau-de-bord\\assets"
        "Copy .png file from \'$($source)\'"

        $SourceDirectoryFilesPNG=gci $source | Where-Object { $_.Extension -eq ".png" }
        $SourceDirectoryFilesPNG |%{
            "`tCopy of \'$($_.Name)\' to \'$($DestinationDirectory6)\'"
		    Copy-Item $_.FullName -Destination "$($DestinationDirectory6)" -Force
            $countPNG++
        }
        "---"
        "$($countPNG) file(s) \'.png\' copied"
	    "---"
    }

} else {
    "Erreur!!! Verifier l\'existence des repertoires source et destination!!"
}
'''
      }
    }

    stage('ERP_H_CopyCommitFile_BuildDirectory') {
      steps {
        powershell '"aaa"'
      }
    }

  }
}