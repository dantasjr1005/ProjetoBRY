#Criando o Cluster GKE com Terraform#

![image](https://github.com/user-attachments/assets/9bf52ab4-7a57-43d4-be51-96abd9ed49bf)

##irei colocar os comentarios na linha de codigo para deixa auto explicativo a criação do cluster , lembrando que o projeto se da a criação de um cluster e o deploy dele utilizando a ferramenta CircleCi

# Definir o provedor do Google Cloud #Sempre verificar a região 
provider "google" {
  credentials = file("keyl.json")   # Arquivo de credenciais da conta de serviço para autenticação
  project     = "original-nation-453118-a7"  # ID do seu projeto no Google Cloud
  region      = "us-central1"        # Região onde o cluster será criado
} 

# Criar um cluster GKE
resource "google_container_cluster" "primary" {
  name     = "my-cluster"            # Nome do cluster
  location = "us-central1-a"         # Zona onde o cluster será criado

  initial_node_count = 3             # Número inicial de nós (máquinas virtuais) no cluster
  min_master_version = "latest"      # Versão do master do cluster

  # Configurações da rede (opcional)
  network    = "default"             # Rede padrão do Google Cloud "default" foi a variavel que eu coloquei 
  subnetwork = "default"             # Sub-rede padrão do Google Cloud

  # Configurações de nós do cluster
  node_config {
    machine_type = "n1-standard-1"   # Tipo de máquina para os nós (n1-standard-1 é uma opção com 1 vCPU e 3.75 GB de RAM)
    oauth_scopes = [
      "https://www.googleapis.com/auth/cloud-platform"   # Permissões necessárias para acessar o Google Cloud
    ]
    disk_size_gb = 50  # Tamanho do disco em GB para cada nó (50 GB no caso) # esse tamanho vai para cada no 
  }

  # Ativar a API de contêiner (opcional)
  enable_legacy_abac = false
}
![image](https://github.com/user-attachments/assets/4be140ec-585c-4f11-94e3-428bc632386c)
essa imagem informa que os NOS estão funcionando 




#Lembrando que ao interagirmos com o console devemos ativar o serviço Kubernetes Engine e ficar atento as permissões da criação do cluster

##Provedor Google Cloud: A primeira parte define o provedor Google Cloud para o Terraform, especificando o arquivo de credenciais (keyl.json), o projeto e a região onde o cluster será criado.

Recurso google_container_cluster: Este recurso cria o cluster GKE com o nome my-cluster na zona us-central1-a. Ele tem 3 nós iniciais e usa a configuração padrão de rede e sub-rede.

Configurações dos Nós: O cluster utilizará máquinas do tipo n1-standard-1 com 50 GB de disco. A permissão cloud-platform garante que o cluster tenha acesso aos recursos do Google Cloud.

Habilitação de ABAC (Attribute-Based Access Control): O parâmetro enable_legacy_abac = false desabilita a configuração de controle de acesso legado.

#Terraform flow init , plan , apply para finalizar o fluxo de criação 

####Com isso criamos um template com terraform , criamos uma conta de serviço , ajustamos as permissoes e o tamanho do disco ..

FAZENDO O DEPLOY NO CIRCLE CIE UTILIZANDO O KUBECTL
![image](https://github.com/user-attachments/assets/d332a246-3b76-4ad4-9dda-c7dcdbf6de5d)

primeiro passo devemos criar o arquivo config.yml para definirmos os nossos parametros 

aqui vai a explicação dos comentarios 

Explicação dos Passos no CircleCI
Imagem Docker: O job deployment usa a imagem gcr.io/google.com/cloudsdktool/google-cloud-cli:alpine, que já tem o gcloud e kubectl pré-instalados, o que facilita a integração com o Google Cloud e o Kubernetes.

Instalação do kubectl: Caso precise, o comando kubectl será instalado. Isso é útil se você estiver criando o ambiente do zero e não tiver o kubectl já presente.

Instalação do Plugin de Autenticação GKE: O gke-gcloud-auth-plugin é instalado para permitir que o kubectl use as credenciais do gcloud para autenticação no GKE.

Chave de Serviço: O valor da variável de ambiente GOOGLE_APPLICATION_CREDENTIALS_JSON (geralmente fornecida pelo CircleCI de forma segura) é decodificado de base64 e salvo como um arquivo JSON temporário (/tmp/gcloud-key.json), para autenticar a conta de serviço.

Autenticação com o Google Cloud: O comando gcloud auth activate-service-account usa o arquivo de chave JSON para autenticar o serviço no Google Cloud. Depois, gcloud container clusters get-credentials recupera as credenciais do cluster GKE para permitir que o kubectl interaja com o cluster.

Login no Cluster: O comando kubectl cluster-info retorna informações sobre o cluster, confirmando que a autenticação e a conexão com o cluster GKE foram bem-sucedidas.

se tudo ocorrer com sucesso sua pipeline vai executar sem erro , caso de errado ela informa a linha e o bloco que deu o erro 
# vou deixar dois exemplos um que concluirmos com sucesso e um outro erro e espficação na sua linha de codigo 

![image](https://github.com/user-attachments/assets/e18f8ac8-9254-4e05-81f1-473881a8976f)

![image](https://github.com/user-attachments/assets/29adce77-b8aa-4b85-9150-f7fb5ba6c36a)

#Comandos kubectl Importantes
#Aqui estão alguns comandos kubectl que você pode usar durante o deploy ou administração do cluster GKE
#kubectl cluster-info
#kubectl get nodes
#kubectl get pods
temos que aprender a gerenciar o fluxo de forma correta 





