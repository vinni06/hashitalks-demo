#!/usr/bin/env groovy
node() {
  timestamps {
    
     
     
     stage ('Create Wrapped Secret ID') {
      def WRAPPED_SID = ""
      env.WRAPPED_SID = sh(
        returnStdout: true,
        script: "vault write -field=wrapping_token -wrap-ttl=200s -f auth/pipeline/role/pipeline-approle/secret-id"
      )
    }
    
    stage ("Get Role ID for the pipeline AppRole") {
      def ROLE_ID = ""
      env.ROLE_ID = sh(
        returnStdout: true,
        script: "vault read -field=role_id auth/pipeline/role/pipeline-approle/role-id"
      )
    }
    stage("Unwrap Secret ID"){
      def UNWRAPPED_SID = ""
      env.UNWRAPPED_SID = sh(
        returnStdout: true,
        script: "vault unwrap -field=secret_id ${WRAPPED_SID}"
      )
    }
    stage("Get login token with Role ID and unwrapped Secret ID"){
      def VAULT_LOGIN_TOKEN = ""
      env.VAULT_LOGIN_TOKEN = sh(
        returnStdout: true,
        script: "vault write -field=token auth/pipeline/login role_id=${ROLE_ID} secret_id=${UNWRAPPED_SID}"
      )
    }
    stage("Login into Vault") {
      def VAULT_TOKEN = ""
      env.VAULT_TOKEN = sh(
        returnStdout: true,
        script: "vault login -field=token ${VAULT_LOGIN_TOKEN}"
      )
    }
    stage("Fetch my kv secrets") {
      sh(returnStdout:false, script: "VAULT_TOKEN=${VAULT_TOKEN} vault read kv/pipeline-secrets")
    }
  }
  
}
