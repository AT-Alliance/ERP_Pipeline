pipeline {
    agent any
    stages {
      environment {
          DirectoryToPurgeEnv = 'C:\\Livrables\\All_dotnet'
          ExcludeFolderEnv = 'SvnFolderForDelivery'
      }
        parallel {
            stage('ERP_B-1_RestoreNuget') {
              steps {
                bat '"%WORKSPACE%\\build\\nuget.exe" restore "%WORKSPACE%\\GCRADC.sln"'
              }
            }
            stage('ERP_B-3_GetLastCommit') {
                environment {
                SvnBinEnv = 'C:\\Program Files\\TortoiseSVN\\bin\\svn'
                SvnRepositoryUrlEnv = 'https://alliance-vm03/svn/ERP_ALLIANCE_ARMAND/trunk'
                BaseOutputRootDirectoryEnv = 'C:\\Livrables'
                BaseOutputDirectoryEnv = 'All_dotnet'
            }
            steps {
                powershell '''
$SvnBin = "$($env:SvnBinEnv)"
$SvnRepositoryUrl = "$($env:SvnRepositoryUrlEnv)"
$BaseOutputDirectory = "$($env:BaseOutputDirectoryEnv)"
$BaseOutputRootDirectory = "$($env:BaseOutputRootDirectoryEnv)"
$BaseOutputDirectory = "$($env:BaseOutputDirectoryEnv)"
$DestinationDirectory = "$($BaseOutputRootDirectory)\\$($BaseOutputDirectory)"
$DestinationDirectoryName = "SvnFolderForDelivery"
if ( -not (Test-Path "$($DestinationDirectory)\\$($DestinationDirectoryName)") -and ($($DestinationDirectoryName) -ne "") ) {
    
    New-Item -ItemType Directory "$($DestinationDirectory)\\$($DestinationDirectoryName)"
    "`nLe repertoire \'$($DestinationDirectoryName)\' inexistant a ete créé dans \'$($DestinationDirectory)\'`n"
    "-------------------------"
}
$DestinationDirectory="$($DestinationDirectory)\\$($DestinationDirectoryName)"
if ( Test-Path $($DestinationDirectory) ) {
    
    try {
        . "$($SvnBin)" info "$($SvnRepositoryUrl)" --username atjenkins --password atjenkins --trust-server-cert  --non-interactive | Out-File "$($DestinationDirectory)\\svn_lastest_commit.txt"
    } catch {
        "An error occurred: $_"
    }
}'''
                }
            }
        }
        

      }
    }
    
