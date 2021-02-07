### Hyperledger Fabric Go SDK patch for Vault crypto storage

This repository contains patch for Fabric BCCSP and fabric-sdk-go own packages. This patch makes it possible to use Vault as users' crypto storage instead of default file-based storage.  

#### Howto
1. apply patch to the sdk
2. make replace of sdk in go.mod
3. add new section to the SDK connection profile:

        BCCSP:
            Default: SW
            SW:
              Hash: SHA2
              Security: 256
              # Location of Key Store
              VaultKeyStore:
                # If "", defaults to 'mspConfigPath'/keystore
                KeyStore: /opt/crypto/peerOrganizations/org/users/User1@org/msp
 
 4. Run Vault. Example:
 
        docker run --network my_net -d --name=dev-vault -p 8200:8200 -e 'VAULT_DEV_ROOT_TOKEN_ID=1' -e 'VAULT_DEV_LISTEN_ADDRESS=0.0.0.0:8200' --cap-add=IPC_LOCK vault               
 
 5. Run client application with environment variables:
   
    - **VAULT_ADDR** - address of Vault server
    - **VAULT_TOKEN** - Vault token
    - **VAULT_SPACE** - root of the path in Vault kv storage (all keys will be in this namespace)
    
    Example:
        
        docker run -d --restart unless-stopped --network my_net --name app \
         -e VAULT_ADDR=http://dev-vault:8200 \
         -e VAULT_TOKEN=1 -e VAULT_SPACE=kv/data awesome-application