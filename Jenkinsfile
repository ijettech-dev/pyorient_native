#!/usr/bin/env groovy
@Library('pipelineLibrary@master') _

def pythonbuild = new ijet.jenkins.PythonBuild()


def build_stages(Map args=[:]) {
    stage('Preparation') {
        prepare(args.project, args.branch)
    }

    args.pyver = '3.6.1'
    stage('Build Component (py36)') {
        build_wheel(args)
    }

    args.pyver = '3.4.6'
    stage('Build Component (py34)') {
        build_wheel(args)
    }

    if (env.BRANCH_NAME =~ /^master/ ) {
        stage('Run Black Duck Scan') {
            build_and_scan(args)
        }
    }
    stage('Upload Component') {
        upload_wheels(args)
    }

    cleanup_stage()
}


node('docker') {
    try {
        build_stages(project:'pyorient-native',
                     init:true,
                     buildContainer:false)
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
