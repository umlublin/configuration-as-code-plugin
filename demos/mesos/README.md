# Configure mesos plugin

Basic configuration of the [Mesos plugin](https://plugins.jenkins.io/mesos)

## sample configuration

```yaml
jenkins:
  clouds:
    - mesos:
        checkpoint: false
        cloudID: "mesos-name"
        credentialsId: "MESOS_CREDENTIALS"
        declineOfferDuration: 600
        description: "My Mesos Cloud"
        frameworkName: "Jenkins Framework"
        # jenkinsURL is a mandatory field, Jenkins fails without it. See https://github.com/jenkinsci/configuration-as-code-plugin/issues/578
        jenkinsURL: "https://jenkins.mesos.cloud"
        master: 1.2.3.4:8000
        nativeLibraryPath: "/usr/lib/libmesos.so"
        onDemandRegistration: true
        principal: "MESOS_PRINCIPAL"
        role: "*"
        slavesUser: "jenkins"
        slaveInfos:
          - labelString: "docker"
            containerInfo:
              type: "DOCKER"
              dockerImage: "cloudbees/java-with-docker-client:latest"
              networking: "BRIDGE"
              dockerForcePullImage: false
              volumes:
                - containerPath: "/var/run/docker.sock"
                  hostPath: "/var/run/docker.sock"
                  readOnly: "false"
                - containerPath: "/tmp/jenkins/workspace/"
                  hostPath: "/tmp/jenkins/workspace/"
                  readOnly: "false"
            mode: "NORMAL"
            slaveCpus: "0.1"
            slaveMem: 512
            executorCpus: "0.1"
            executorMem: 128
            diskNeeded: "0.0"
            minExecutors: 1
            maxExecutors: 2
            remoteFSRoot: "jenkins"
            jvmArgs: ""
            jnlpArgs: ""
            defaultSlave: true
            idleTerminationMinutes: 5
            slaveAttributes: >
              {"rack":"jenkins-build-agents"}
```

## implementation note

Jenkins singleton doesn't offer any `setClouds` method. So here we rely on a pseudo-property implemented by a dedicated
`Attribute` to add the configured clouds to `Jenkins.clouds`. The current implementation only adds the configured cloud
if it doesn't exists yet.
