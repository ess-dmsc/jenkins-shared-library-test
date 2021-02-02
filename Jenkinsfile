@Library('ecdc-pipeline-test')
import ecdcpipeline.PipelineBuilder
import ecdcpipeline.ContainerBuildNode

container_build_nodes = [
  'centos7': ContainerBuildNode.getDefaultContainerBuildNode('centos7-gcc8'),
  'centos7-release': ContainerBuildNode.getDefaultContainerBuildNode('centos7-gcc8'),
  'debian10': ContainerBuildNode.getDefaultContainerBuildNode('debian10'),
  'ubuntu1804': ContainerBuildNode.getDefaultContainerBuildNode('ubuntu1804-gcc8')
]

pipeline_builder = new PipelineBuilder(this, container_build_nodes)

builders = pipeline_builder.createBuilders { container ->
  pipeline_builder.stage("${container.key}: checkout") {
    dir(pipeline_builder.project) {
      scm_vars = checkout scm
    }
    // Copy source code to container
    container.copyTo(pipeline_builder.project, pipeline_builder.project)
  }  // stage

  pipeline_builder.stage("${container.key}: get dependencies") {
    container.sh """
      pwd
      ls
    """
  }  // stage
}

timeout(time: 1, unit: 'HOURS') {
  node('docker') {
    parallel builders
  }
  pipeline_builder.archiveBuildInfo()
}
