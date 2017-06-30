env.DIST = 'xenial'
env.TYPE = 'user'
env.PWD_BIND = '/workspace'

node {
  sh 'ls -lah'
  sh 'env'
  sh 'false'
}

cleanNode('os-autoinst') {
  try {
    stage('tree[wget]') {
      sh "wget -q http://metadata.neon.kde.org/os-autoinst/${env.TYPE}.tar"
      sh "wget -q http://metadata.neon.kde.org/os-autoinst/${env.TYPE}.tar.sig"
    }
    parallel(
      "tree[verify]": {
        sh 'gpg2 --recv-keys "348C 8651 2066 33FD 983A  8FC4 DEAC EA00 075E 1D76"'
        sh 'gpg2 --verify ${TYPE}.tar.sig'
      },
      "tree[untar]": {
        sh 'tar -xf ${TYPE}.tar'
        sh 'ls -lah'
      }
    )
    stage('pull') {
      sh 'git branch --set-upstream-to=origin/master master'
      sh 'git pull'
    }
    stage('rake-test') {
      sh 'rake test'
    }
    stage('test-plasma_folder') {
      sh 'TESTS_TO_RUN=tests/plasma_folder.pm bin/contain.rb /workspace/bin/bootstrap.rb'
    }
  } finally {
    archiveArtifacts 'wok/testresults/*.png, wok/testresults/*.json, wok/ulogs/*, wok/video.ogv, wok/vars.json'
    junit 'junit/*'
    sh 'bin/contain.rb chown -R jenkins . || true'
  }
}
