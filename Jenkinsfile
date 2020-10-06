@Library('ecdc-pipeline-test')
import ecdcpipeline.ImageRemover

def names = [
  'dmbuild01.dm.esss.dk',
  'dmbuild02.dm.esss.dk',
  'dmbuild05.dm.esss.dk',
  'dmbuild06.dm.esss.dk',
  // 'dmbuild07.dm.esss.dk',
  // 'dmbuild08.dm.esss.dk',
  // 'dmbuild09.dm.esss.dk',
  // 'dmbuild10.dm.esss.dk',
  // 'dmbuild11.dm.esss.dk',
  // 'dmbuild20.dm.esss.dk',
  // 'dmbuild21.dm.esss.dk',
  // 'dmbuild22.dm.esss.dk',
  // 'dmbuild23.dm.esss.dk',
  // 'dmbuild24.dm.esss.dk',
  // 'dmbuild25.dm.esss.dk',
  // 'dmbuild26.dm.esss.dk',
  // 'systest01.dm.esss.dk',
  // 'systest02.dm.esss.dk'
]

imageRemover = new ImageRemover(this)

def builders = [:]
for (x in names) {
  def name = x
  builders[name] = {
    node(name) {
      checkout scm

      stage('Remove Docker Images') {
        imageRemover.cleanImages()
      }

      cleanWs()
    }
  }
}

timeout(time: 1, unit: 'HOURS') {
  parallel builders
}
