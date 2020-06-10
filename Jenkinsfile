@Library('ecdc-pipeline-test')
import ecdcpipeline.ContainerBuildNode
import ecdcpipeline.PipelineBuilder

containerBuildNodes = [
  'centos': ContainerBuildNode.getDefaultContainerBuildNode('centos7'),
  'ubuntu': ContainerBuildNode.getDefaultContainerBuildNode('ubuntu1804')
]

pipelineBuilder = new PipelineBuilder(this, containerBuildNodes, "/home/jenkins/data:/var/data")
pipelineBuilder.activateEmailFailureNotifications()

builders = pipelineBuilder.createBuilders { container ->

  pipelineBuilder.stage("${container.key}: Checkout") {
    dir(pipelineBuilder.project) {
      scm_vars = checkout scm
    }
    container.copyTo(pipelineBuilder.project, pipelineBuilder.project)
  }  // stage

  pipelineBuilder.stage("${container.key}: Dependencies") {
    def conan_remote = "ess-dmsc-local"
    container.sh """
      mkdir build
      cd build
      conan remote add \
        --insert 0 \
        ${conan_remote} ${local_conan_server}
      conan install --build=outdated ../${pipelineBuilder.project}/conanfile.txt
    """
  }  // stage

}

// Checkout code on coordinator node and start builders in parallel.
node {
  echo "Add message 2"
  checkout scm
  pipelineBuilder.abortBuildOnMagicCommitMessage()
  try {
    parallel builders
    } catch (e) {
      pipelineBuilder.handleFailureMessages()
      throw e
  }
}
