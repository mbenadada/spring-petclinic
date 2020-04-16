#!groovy
import hudson.model.*
pipeline {
    agent any
    stages {
        stage('build') {
            steps {
                script {
                    def String last_success_hash = ""
		    def fileName = "${JENKINS_HOME}" + "\\jobs\\" + "${JOB_NAME}".substring(0, "${JOB_NAME}".indexOf("/")) + "\\branches\\" + "${BRANCH_NAME}" + "\\success_hash.txt"
                    try{
                        last_success_hash = readFile fileName	
                    }
                    catch(Exception exp){
                    	println ">>>>>>>>>>>>>>************ no success build yet"
                    }
	            if ("".equals(last_success_hash)) {// first build
	            	try{
	            	   println ">>>>>>>>>>>>>>************ first build (file success_hash absent): run mvnw package"
		           sh './mvnw package'
			   def successHash = "${env.GIT_COMMIT}"
	    	           writeFile file: fileName, text: successHash
	            	   println ">>>>>>>>>>>>>>************ first build completed successfully and success_hash created, hash: " + successHash
		        }catch(Exception exp){
	            	   println ">>>>>>>>>>>>>>************ Tests did not pass! check your last commit"
			   sh 'exit 1'
		        }
	            }else{
	            	println ">>>>>>>>>>>>>>************ success_hash found and last success hash read: " + last_success_hash
	                def gitCommandCountBuilds  = "git rev-list origin/master..." + last_success_hash + " | wc -l"
	                def count = sh(script: gitCommandCountBuilds, returnStdout: true).toString().trim()
	            	println ">>>>>>>>>>>>>>************ number of commits since last build: " + count
	                if(Integer.parseInt(count)>=8){
	            	    try{
		            	println ">>>>>>>>>>>>>>************ number of commits >= 8: run mvnw package"
		                sh './mvnw package'
			        def successHash = "${env.GIT_COMMIT}"
	    	                writeFile file: fileName, text: successHash
		            }catch(Exception exp){
	            	        println ">>>>>>>>>>>>>>************ Tests did not pass! git bisect to indentify first commit causing this situation"
				def badRevision  = "${GIT_COMMIT}"
				def goodRevision  = last_success_hash
				def gitBisectStartCommand  = "git bisect start " + badRevision + " " + goodRevision
				def gitBisectRunCommand  = "git bisect run ./mvnw clean test"
				sh gitBisectStartCommand
				sh gitBisectRunCommand
			        sh 'exit 1'
		            }
	                }else{
	            	    println ">>>>>>>>>>>>>>************ less than 8 commits since last build, do nothing"
	                }
	            }
                }
            }
        }   
    }    
}//20200415_1949
//20200415_1959
//20200415_2016
//20200415_2017
//20200415_2018
