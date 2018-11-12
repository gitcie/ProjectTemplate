pipeline{
  agent any
  stages {
      stage('Compile') {
        steps {
          // Compile the app and its dependencies
          sh 'gradle compileDebugSources'
        }
      }
      stage('Build APK') {
        steps {
          // Finish building and packaging the APK
          sh 'gradle assembleDebug'

          // Archive the APKs so that they can be downloaded from Jenkins
          archiveArtifacts '**/*.apk'
        }
      }
      stage('Deploy') {
        when {
          // Only execute this stage when building from the `beta` branch
          not {branch 'master'}
        }
        environment {
          // Assuming a file credential has been added to Jenkins, with the ID 'my-app-signing-keystore',
          // this will export an environment variable during the build, pointing to the absolute path of
          // the stored Android keystore file.  When the build ends, the temporarily file will be removed.
          SIGNING_KEYSTORE = credentials('android-sign-keystore')

          // Similarly, the value of this variable will be a password stored by the Credentials Plugin
          SIGNING_KEY_PASSWORD = credentials('android')
        }
        steps {
          // Build the app in release mode, and sign the APK using the environment variables
          sh 'gradle assembleRelease'

          // Archive the APKs so that they can be downloaded from Jenkins
          archiveArtifacts '**/*.apk'

          // Upload the APK to Google Play
          androidApkUpload googleCredentialsId: 'Google Play', apkFilesPattern: '**/*-release.apk', trackName: 'beta'
        }
        //post {
          //success {
            // Notify if the upload succeeded
            //mail to: 'lusiyi@szboanda.net', subject: 'New build available!', body: 'Check it out!'
          //}
        //}
      }
    }
}
