#!/usr/bin/groovy

podTemplate(label: 'twistlock-example-builder', // See 1
  containers: [
    containerTemplate(
      name: 'jnlp',
      image: 'jenkinsci/jnlp-slave:3.10-1-alpine',
      args: '${computer.jnlpmac} ${computer.name}'
    ),
    containerTemplate(
      name: 'alpine',
      image: 'twistian/alpine:latest',
      command: 'cat',
      ttyEnabled: true
    ),
  ],
  volumes: [ // See 2
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'), // See 3
  ]
)
{
  node ('twistlock-example-builder') {

    stage ('Pull image') { // See 4
      container('alpine') {
        sh """
        curl --unix-socket /var/run/docker.sock \ // See 5
             -X POST "http:/v1.24/images/create?fromImage=nginx:stable-alpine"
        """
      }
    }

    stage ('Prisma Cloud scan') { // See 6
        twistlockScan ca: '',
                    cert: '',
                    compliancePolicy: 'critical',
                    dockerAddress: 'unix:///var/run/docker.sock',
                    gracePeriodDays: 0,
                    ignoreImageBuildTime: true,
                    image: 'nginx:stable-alpine',
                    key: '',
                    logLevel: 'true',
                    policy: 'warn',
                    requirePackageUpdate: false,
                    timeout: 10
    }

    stage ('Prisma Cloud publish') {
        twistlockPublish ca: '',
                    cert: '',
                    dockerAddress: 'unix:///var/run/docker.sock',
                    ignoreImageBuildTime: true,
                    image: 'nginx:stable-alpine',
                    key: '',
                    logLevel: 'true',
                    timeout: 10
    }
  }
}
