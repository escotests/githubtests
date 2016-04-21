def getShortenedWorkspace(def wsMaxLength = 65) {  
  echo 'Shortening workspace: ' + pwd()
  workspace = pwd().minus('bugfix%2F').minus('feature%2F').minus('hotfix%2F')
  echo 'Removed branch prefixes'
  echo "${workspace.length() > wsMaxLength}"
  echo workspace[0..wsMaxLength]
  echo workspace
  workspace = (workspace.length() > wsMaxLength) ? workspace[0..wsMaxLength] : workspace
  echo "Trimmed to ${wsMaxLength}"
  return workspace  
}

stage concurrency: 1, name: 'Build'
	node {
	  deleteDir()
	  ws(getShortenedWorkspace(65)) {
		sh 'pwd'	  
	  
	  } 
	}
