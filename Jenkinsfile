@Library('ecdc-pipeline-test')
import ecdcpipeline.ContainerBuildNode
import ecdcpipeline.PipelineBuilder

containerBuildNodes = [
  'centos-debug': ContainerBuildNode.getDefaultContainerBuildNode('centos7'),
  'centos-release': ContainerBuildNode.getDefaultContainerBuildNode('centos7'),
  'ubuntu': new ContainerBuildNode('essdmscdm/ubuntu18.04-build-node:1.1.0', 'bash -e')
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
      conan install --build=outdated ../${pipelineBuilder.project}/conan/conanfile.txt
    """
  }  // stage

}

// Checkout code on coordinator node and start builders in parallel.
node {
  checkout scm
  try {
    parallel builders
    } catch (e) {
      pipelineBuilder.handleFailureMessages()
      throw e
  }
}
