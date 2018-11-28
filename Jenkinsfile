#!/usr/bin/env groovy
@Library('pipelineLibrary@master') _

def pythonbuild = new ijet.jenkins.PythonBuild()


node('docker') {
    try {
        Map args = [project: 'pyorient-native', init: true, buildContainer: false]

        stage('Preparation') {
            pythonbuild.prepare(args.project, args.branch)
        }

        args.pyver = '3.6.1'
        stage('Build Component (py36)') {
            pythonbuild.build_wheel(args)
        }

        args.pyver = '3.4.6'
        stage('Build Component (py34)') {
            pythonbuild.build_wheel(args)
        }

        if (env.BRANCH_NAME =~ /^master/ ) {
            stage('Run Black Duck Scan') {
                pythonbuild.build_and_scan(args)
            }
        }
        stage('Upload Component') {
            pythonbuild.upload_wheels(args)
        }

        pythonbuild.cleanup_stage()
        pythonbuild.succeed()
    }
    catch (err) {
        pythonbuild.fail()
        throw err
    }
    finally {
        pythonbuild.cleanup()
    }
}
