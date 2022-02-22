#!groovy


def hook
def testingFarmRequestId


pipeline {

    // The pipeline doesn't need an agent, but individual stages can have one if they need
    agent none

    libraries {
        // Library version is pinned down to make sure that things don't break unexpectedly
        lib("fedora-pipeline-library@52e1c8a21798e61f4bf186dfd79092d147db76f2")
    }

    environment {
        TESTING_FARM_API_KEY = credentials('testing-farm-api-key')
        FEDORA_CI_TESTING_FARM_API_URL = "https://api.dev.testing-farm.io"
        FEDORA_CI_TESTING_FARM_ARTIFACTS_URL = "http://artifacts.dev.testing-farm.io"
    }

    stages {
        stage('Schedule Test') {
            agent {
                // We can run this stage on a proper agent, if we want
                label 'dist-git'
            }

            steps {
                script {
                    // Create webhook in Jenkins where we will receive updates from Testing Farm
                    hook = registerWebhook()

                    // https://testing-farm.gitlab.io/api/#operation/requestsPost
                    def requestPayload = [
                        api_key: env.TESTING_FARM_API_KEY,
                        test: [
                            fmf: [
                                url: "https://src.fedoraproject.org/rpms/vim",
                                ref: "bea8236b360616fe67569ea9de27c887d36fb9f8"
                            ]
                        ],
                        environments: [
                            [
                                arch: "x86_64",
                                os: [
                                    compose: "Fedora-Rawhide"
                                ],
                                tmt: [
                                    context: [
                                        arch: "x86_64",
                                        distro: "fedora-37",
                                        trigger: "build"
                                    ]
                                ],
                                artifacts: [
                                    [
                                        id: "83123680",  // Koji Task ID
                                        type: "fedora-koji-build"
                                    ]
                                ]
                            ]
                        ],
                        notification: [
                            webhook: [
                                url: hook.getURL()
                            ]
                        ]
                    ]

                    def response = submitTestingFarmRequest(payloadMap: requestPayload)
                    testingFarmRequestId = response['id']
                }
            }
        }

        stage('Wait for Test Results') {

            // Don't occupy an executor while waiting for Testing Farm to finish
            agent none

            steps {
                script {
                    def response = waitForTestingFarm(requestId: testingFarmRequestId, hook: hook)
                    echo "Done!"
                    echo "${response.apiResponse}"
                }
            }
        }
    }
}
