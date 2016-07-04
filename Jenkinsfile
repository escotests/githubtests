/*node {
        stage 'Load a file from GitHub'
        def helloworld = fileLoader.fromGit('examples/fileLoader/helloworld', 
                'https://github.com/escoem/workflow-remote-loader-plugin.git', 'master', null, '')
        
        stage 'Run method from the loaded file'


    helloworld.printHello()
    //git 'https://github.com/escotests/githubtests.git'
    checkout scm
    
    def changeLogSets = currentBuild.rawBuild.changeSets
    for (int i = 0; i < changeLogSets.size(); i++) {
        def entries = changeLogSets[i].items
        for (int j = 0; j < entries.length; j++) {
            def entry = entries[j]
            echo "${entry.commitId} by ${entry.authorEmail} on ${new Date(entry.timestamp)}: ${entry.msg}"
        }
    }
    helloworld.printHello()
}*/

// repo1
def ENV.cdProjectURL = "https://github.com/escoem/workflow-remote-loader-plugin.git"
def ENV.cdProjectBranch = "master"
//def env.gitAuthCredential 
// repo2
def ENV.appProjectURL = "https://github.com/escotests/githubtests.git"
def ENV.appProjectBranch "bran"
//def env.gitAuthCredential 

def ENV.cdCommonPropertiesLoc = "examples/fileLoader/environment.groovy"
def ENV.cdBuildPropertiesLoc = "examples/fileLoader/helloworld.groovy"
def ENV.cdAppPropertiesLoc = "myExternalMethod.groovy"

node { 
        def cdBuildProperties, cdCommonProperties, cdAppProperties 
        stage 'Setup' 
        // use for loading common pipeline code properties from folder level env. variables 
        fileLoader.withGit( "${ENV.cdProjectURL}", "${ENV.cdProjectBranch}", null, "") { 
                cdCommonProperties = fileLoader.load("${ENV.cdCommonPropertiesLoc}"); 
                cdBuildProperties = fileLoader.load("${ENV.cdBuildPropertiesLoc}"); 
        }

        stage 'Checkout' 
        // use for checking out app source code 
        customCheckout( null,"${ENV.appProjectURL}", "${ENV.appProjectBranch}") 
        cdAppProperties= load("${ENV.cdAppPropertiesLoc}");

        stage 'RTLNotification' 
        // use for sending mail notification 
        devBuildNotification(cdAppProperties.getReleaseNumber()) 
}

def customCheckout(def sourceCodeRepoCredentials, def sourceCodeRepoURL, def branch){ 
        git url:"${sourceCodeRepoURL}", branch:"${branch}" 
}

def devBuildNotification(def releaseNumber){ 
        //to get commit change logs in table format to send in notification 
        String changeSets = getChangesets() 
}

@NonCPS 
def getChangesets(){ 
        String commitChanges =""; 
        def changeLogSets = currentBuild.rawBuild.changeSets 
        changeLogSets.every{changeLogSet -> 
                changeLogSet.every{entries -> 
                        entries.every{ entry -> 
                                commitChanges +="<tr>" 
                                entry.every{it-> 
                                        String appCommitIdURL = "${env.appProjectURL}".split("@")[1].replace(":", "/").split(".git")[0]+"/commit/${it.commitId}" 
                                        appCommitIdURL = "https://${appCommitIdURL}" 
                                        commitChanges += "<td style=\"text-align: center; border: 1px solid black;\"><a href='\"${appCommitIdURL}\"'>${it.commitId}</a></td>" 
echo "${appCommitIdURL}" 
                                        commitChanges += "<td style=\"text-align: center; border: 1px solid black;\">${it.author}</td>" 
                                        java.util.Date date = new java.util.Date(it.timestamp ); 
                                        commitChanges += " <td style=\"text-align: center; border: 1px solid black;\">${date}</td>" 
                                        commitChanges +="<td style=\"text-align: center; border: 1px solid black;\">${it.msg}</td>" 
                                        commitChanges +="<td style=\"text-align: center; border: 1px solid black;\">" 
                                        it.affectedFiles.every{file-> 
                                                commitChanges += "${file.path} " 
                                        } 
                                        commitChanges += "</td></tr>" 
                                } 
                        } 
                } 
        } 
        String commitChangesTable="" 
        if(commitChanges != ""){ 
                String commitChangesTitle = "<H3>ChangeLogs: </H3>" 
                commitChangesTable = commitChangesTitle +"<TABLE style=\"width:100%; border: 1px solid black;border-collapse: collapse\"><tr style=\"background-color: yellowgreen\"><th style= \" border: 1 px solid black;\">Commit Id</th><th style = \" border: 1 px solid black;\">Author</th><th style = \" border: 1 px solid black;\">Commit Date</th><th style = \" border: 1 px solid black;\">Commit Message</th><th style = \" border: 1 px solid black;\">Fileset</th></tr>" + "$commitChanges" + "</TABLE>" 
        } 
        return commitChangesTable; 
} 
