node {
  checkout scm
  stash includes: 'sources/**/*', name: 'sources'
  stage('Build') {
    docker.image('python:2-alpine').inside {
      sh 'python -m py_compile sources/add2vals.py sources/calc.py'
    }
  }
  stage('Test') {
    docker.image('qnib/pytest').inside {
      sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
    }
    junit 'test-reports/results.xml'
  }
  stage('Manual Approval') {
    input message: 'Lanjutkan ke tahap Deploy?'
  }
}

node('ubuntu-ec2') {
  unstash 'sources'
  stage('Deploy') {
    docker.image('six8/pyinstaller-alpine-linux-amd64:alpine-3.12-python-2.7-pyinstaller-v3.4').inside("--entrypoint=''") {
      sh 'pyinstaller --onefile sources/add2vals.py'
      archiveArtifacts 'dist/add2vals'
      sleep time: 1, unit: 'MINUTES'
      echo 'Pipeline has finished successfully.'
    }
  }
}
