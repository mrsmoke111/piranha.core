pipeline {
   agent any
   options { 
        skipDefaultCheckout()
   }
   parameters {
      string (
         defaultValue: 'd:/zz-program',
         description: 'Path to nuget.exe',
         name : 'NUGET_EXE_LOCATION')
      string (
         defaultValue: '',
         description: 'Private NuPkg repository on MS Azure',
         name : 'PRIVATE_NUPGK_REPOSITORY_URL')
      string (
         defaultValue: '',
         description: 'Private NuPkg repository username (PAT) on MS Azure',
         name : 'PRIVATE_NUPGK_REPOSITORY_USERNAME')
      string (
         defaultValue: '',
         description: 'Private NuPkg repository password on MS Azure',
         name : 'PRIVATE_NUPGK_REPOSITORY_PASSWORD')                     
   }
    
   stages {
      stage('Preparation') {
         steps {
            checkout scm
         }
      }

      stage('Build') {
         steps {
            bat 'dotnet restore'
            bat 'dotnet clean'
            bat 'dotnet build -c Release'
            bat 'dotnet pack core/Piranha/Piranha.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack core/Piranha.AspNetCore/Piranha.AspNetCore.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack core/Piranha.AspNetCore.SimpleSecurity/Piranha.AspNetCore.SimpleSecurity.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack core/Piranha.AttributeBuilder/Piranha.AttributeBuilder.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack core/Piranha.Azure.BlobStorage/Piranha.Azure.BlobStorage.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack core/Piranha.ImageSharp/Piranha.ImageSharp.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack core/Piranha.Local.FileStorage/Piranha.Local.FileStorage.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack core/Piranha.Manager/Piranha.Manager.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack core/Piranha.Manager.LocalAuth/Piranha.Manager.LocalAuth.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack core/Piranha.Manager.Localization/Piranha.Manager.Localization.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack core/Piranha.Manager.Summernote/Piranha.Manager.Summernote.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack core/Piranha.Manager.TinyMCE/Piranha.Manager.TinyMCE.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack core/Piranha.WebApi/Piranha.WebApi.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack data/Piranha.Data.EF/Piranha.Data.EF.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack data/Piranha.Data.EF.MySql/Piranha.Data.EF.MySql.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack data/Piranha.Data.EF.PostgreSql/Piranha.Data.EF.PostgreSql.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack data/Piranha.Data.EF.SQLite/Piranha.Data.EF.SQLite.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack data/Piranha.Data.EF.SQLServer/Piranha.Data.EF.SQLServer.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack identity/Piranha.AspNetCore.Identity/Piranha.AspNetCore.Identity.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack identity/Piranha.AspNetCore.Identity.MySQL/Piranha.AspNetCore.Identity.MySQL.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack identity/Piranha.AspNetCore.Identity.PostgreSQL/Piranha.AspNetCore.Identity.PostgreSQL.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack identity/Piranha.AspNetCore.Identity.SQLite/Piranha.AspNetCore.Identity.SQLite.csproj --no-build -c Release -o ./artifacts'
            bat 'dotnet pack identity/Piranha.AspNetCore.Identity.SQLServer/Piranha.AspNetCore.Identity.SQLServer.csproj --no-build -c Release -o ./artifacts'
         }
      }
      
      stage('Publish') {
         steps {
            dir("artifacts"){
               bat '''
                  "%NUGET_EXE_LOCATION%/NuGet.exe" sources Add -Name "NL_Feed" -Source "%PRIVATE_NUPGK_REPOSITORY_URL%" -username "%PRIVATE_NUPGK_REPOSITORY_USERNAME%" -password "%PRIVATE_NUPGK_REPOSITORY_PASSWORD%"
                  FOR %%F in (*.nupkg) DO (
                     "%NUGET_EXE_LOCATION%/NuGet.exe" push -Source "NL_Feed" -ApiKey az "./%%F"
                     IF %ERRORLEVEL% NEQ 0 (
                        REM EXIT CODE 409 = FILE ALREADY EXISTS
                        IF %ERRORLEVEL% NEQ 409 (
                           EXIT /B %ERRORLEVEL%
                        )  
                     )
                  )
                  EXIT /B 0
               '''
            }
            bat '"%NUGET_EXE_LOCATION%/NuGet.exe" sources remove -name "NL_Feed"'
         }
      }
   }

   post {
      always {
         dir("artifacts"){
            deleteDir()
         }
         bat '''
            "%NUGET_EXE_LOCATION%/NuGet.exe" sources remove -name "NL_Feed"
            EXIT /B 0
         '''
      }
   }

 }