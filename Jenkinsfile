pipeline {
    def application = params.APPLICATION
    def priority = params.PRIORITY
    def feature = params.FEATURE
    def framework = params.FRAMEWORK
    def scope = params.SCOPE
    def environment = "TEST"
    //def commitRequirement = params.COMMIT_REQUIREMENT
    def commitRequirement
    def tags = "";
    
    stage('Resolve tags') {
        println("APPLICATION: ${application}")
        println("PRIORITY: ${priority}")
        println("FEATURE: ${feature}")
        println("FRAMEWORK: ${framework}")
        println("SCOPE: ${scope}")
        //println("ENVIRONMENT: ${environment}")
        //println("COMMIT_REQUIREMENT: ${commitRequirement}")
        //sh 'mvn --version'
    }
    
    stage('Find the requirement') {
        def response = git 'https://github.com/QAHeroes/MyFirstApp/'
        def commit = sh(returnStdout: true, script: 'git log -1 --oneline').trim()
        commitRequirement = commit.split(" ")[1].split(":")[0]
        println("COMMIT_REQUIREMENT: ${commitRequirement}")
    }
    
    stage('Build the application') {
        println('Building the application...')
        println('Built the application...')
    }
    
    stage('Deploy the application in TEST') {
        println('Deploying the application in TEST...')
        println('Deployed the application in TEST...')
    }
    
    stage('Resolving tests for requirements') {
        println('Resolving tests for requirements using QTest API...')
        //def response = httpRequest "http://httpbin.org/response-headers?APPLICATION=${application}&FEATURE=${feature}&SCOPE=${scope}&PRIORITY=${priority}&ENVIRONMENT=${environment}"
        //println('Status: ' + response.status)
        //println('Response: ' + response.content)
        //def response = httpRequest url: "https://dskhan.qtestnet.com/api/v3/projects/119850/requirements?page=1&size=50", customHeaders: [[name: 'Authorization', value: "bearer 2497bdfe-2080-4b08-91ce-eb42b915f6fd"]]
        def response = httpRequest url: "https://digy360.qtestnet.com/api/v3/projects/7974/requirements?page=1&size=50", customHeaders: [[name: 'Authorization', value: "bearer 19989c73-8593-4703-8c93-a13efae89447"]]
        println('Status: ' + response.status)
        //println('Response: ' + response.content)
        def json = readJSON text: response.content
        def reqId;
        json.each { record ->
            if (record.pid == commitRequirement) {
                reqId = record.id
            }
        }
        println("Req id: " + reqId)

        //def responseRQ = httpRequest url: "https://dskhan.qtestnet.com/api/v3/projects/119850/linked-artifacts?type=requirements&ids=${reqId}", customHeaders: [[name: 'Authorization', value: "bearer 2497bdfe-2080-4b08-91ce-eb42b915f6fd"]]
        def responseRQ = httpRequest url: "https://digy360.qtestnet.com/api/v3/projects/7974/linked-artifacts?type=requirements&ids=${reqId}", customHeaders: [[name: 'Authorization', value: "bearer 19989c73-8593-4703-8c93-a13efae89447"]]
        println('Status: ' + responseRQ.status)
        //println('Response: ' + responseRQ.content)

        def testCasesJson = readJSON text: responseRQ.content
        testCasesJson[0].objects.each { record ->
            tags = tags + "@" + record.pid + " "
        }
        println("Tags: " + tags)
    }

    stage('Running BDD tests for native apps - TEST') {
        //git 'https://github.com/jglick/simple-maven-project-with-tests.git'
        println("Running BDD tests for native apps using maven in TEST...")
        println("mvn clean verify -Ddigy4.framework=cucumber-appium -Dcucumber.filter.tags=\"${tags} @${application} @${priority} @${feature} @${scope} @${environment}\"")
        println("Finished running BDD tests for native apps using maven in TEST...")
    }

    stage('Running BDD tests for Web - TEST') {
        //git 'https://github.com/jglick/simple-maven-project-with-tests.git'
        println("Running BDD tests for Web using maven in TEST...")
        println("mvn clean verify -Ddigy4.framework=cucumber-selenium -Dcucumber.filter.tags=\"${tags} @${application} @${priority} @${feature} @${scope} @${environment}\"")
        println("Finished running BDD tests for Web using maven in TEST...")
    }

    stage('Running Tosca tests - TEST') {
        println("Invoking tosca tests using Tricentis CI jenkins plugin by passing testEvents/executionLists for TEST...")
        println("tricentisCI tricentisClientPath: \"<tosca commander client path>\" testEvents: \"${tags} ${application};${priority};${feature};${scope};${environment}\" endpoint: \"<tosca endpoint>\"")
        println("Finished running TOSCA tests in TEST...")
    }

    stage('Deploy the application in PROD') {
        environment = "PROD"
        println('Deploying the application in PROD...')
        println('Deployed the application in PROD...')
    }
    
    stage('Running BDD tests for native apps - PROD') {
        //git 'https://github.com/jglick/simple-maven-project-with-tests.git'
        println("Running BDD tests for native apps using maven in PROD...")
        println("mvn clean verify -Ddigy4.framework=cucumber-appium -Dcucumber.filter.tags=\"${tags} @${application} @${priority} @${feature} @${scope} @${environment}\"")
        println("Finished running BDD tests for native apps using maven in PROD...")
    }

    stage('Running BDD tests for Web - PROD') {
        //git 'https://github.com/jglick/simple-maven-project-with-tests.git'
        println("Running BDD tests for Web using maven in PROD...")
        println("mvn clean verify -Ddigy4.framework=cucumber-selenium -Dcucumber.filter.tags=\"${tags} @${application} @${priority} @${feature} @${scope} @${environment}\"")
        println("Finished running BDD tests for Web using maven in PROD...")
    }

    stage('Running Tosca tests - PROD') {
        println("Invoking tosca tests using Tricentis CI jenkins plugin by passing testEvents/executionLists for PROD...")
        println("tricentisCI tricentisClientPath: \"<tosca commander client path>\" testEvents: \"${tags} ${application};${priority};${feature};${scope};${environment}\" endpoint: \"<tosca endpoint>\"")
        println("Finished running TOSCA tests in PROD...")
    }

}
