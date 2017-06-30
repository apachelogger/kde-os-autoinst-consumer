env.DIST = 'xenial'
env.PWD_BIND = '/workspace'

openQANode {
  stage('clone') {
    sh 'mkdir consumer'
    dir('consumer') {
        sh 'env'
        sh 'pwd'
        git branch: env.BRANCH_NAME, url: 'https://github.com/apachelogger/kde-os-autoinst-consumer'
        sh 'ls -lah'
    }
  }
  stage('tree[wget]') {
    sh 'ls -lah'
    sh "wget -q http://metadata.neon.kde.org/os-autoinst/${env.TYPE}.tar"
    sh "wget -q http://metadata.neon.kde.org/os-autoinst/${env.TYPE}.tar.sig"
  }
  parallel(
    "tree[verify]": {
      sh 'gpg2 --recv-keys "348C 8651 2066 33FD 983A  8FC4 DEAC EA00 075E 1D76"'
      sh "gpg2 --verify ${env.TYPE}.tar.sig"
    },
    "tree[untar]": {
      sh "tar -xf ${env.TYPE}.tar"
      sh 'ls -lah'
    }
  )
  stage('tree-pull') {
    sh 'git branch --set-upstream-to=origin/master master'
    sh 'git pull'
  }
  stage('test-merge') {
    sh 'cp -rv consumer/* .'
  }
  stage('rake-test') {
    sh 'rake test'
  }
  stage('test-plasma_folder') {
    wrap([$class: 'LiveScreenshotBuildWrapper', fullscreenFilename: 'wok/qemuscreenshot/last.png']) {
      sh 'TESTS_TO_RUN=tests/plasma_folder.pm bin/contain.rb /workspace/bin/bootstrap.rb'
    }
  }
}
