#!groovy

def PARAMS = [
        SRC_URL           : 'github.com',
        SRC_CREDENTIALS_ID: 'pavel-mirror-token',
        SRC_ORG_NAME      : 'disfinder',

        DST_URL           : 'github.com',
        DST_CREDENTIALS_ID: 'pavel-mirror-token',
        DST_ORG_NAME      : 'disfinder',
]

// using list of maps here because mocking POC inside one single server (GitHub)
// in real case with different src and dst servers if repo names should be the same,
// a simple list will work better:
//def REPOS_LIST = [
//        'hello-src',
//        'a-real-repo-01',
//        'a-real-repo-02',
//]

def REPOS_LIST = [
        [
                src: 'hello-src',
                dst: 'hello-dst'
        ],

]

node() {
    deleteDir()
    REPOS_LIST.each { repository ->
        stage(repository.src) {
            dir(repository.src) {
                withCredentials([string(credentialsId: PARAMS.SRC_CREDENTIALS_ID, variable: 'GITHUB_TOKEN')]) {
                    def srcGitUrl = "https://${GITHUB_TOKEN}@${PARAMS.SRC_URL}/${PARAMS.SRC_ORG_NAME}/${repository.src}.git"
                    sh "git clone ${srcGitUrl} . --mirror"
                }
                withCredentials([string(credentialsId: PARAMS.DST_CREDENTIALS_ID, variable: 'GITHUB_TOKEN')]) {
                    def dstGitUrl = "https://${GITHUB_TOKEN}@${PARAMS.DST_URL}/${PARAMS.DST_ORG_NAME}/${repository.dst}.git"
                    sh "git remote add dst ${dstGitUrl}"
                    sh "git push dst --mirror --dry-run" //remove dry-run to do an actual sync
                }
            }
        }
    }
}
