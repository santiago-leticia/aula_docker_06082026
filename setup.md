# m Grupo de Recursos para abrigar os recursos que serão criado
# Primeiro passo fizemos o criacao das exportamos os dockerfilhes e longo apos execultados no fizemos a criação da nossa imagem de docker
# vamos fazer um serviço de docker
# vai se feito 3 container dentro deles 


# depois fizemos um serviço apos de fazer tudo local e apos disso colocar tudo em um servidor
# agora so faz push, so jogar para cima

# Chamado de Paas - voce so criar e usar, so dar 3 comados e ja utilizar e sim é util 

# agora é automatizar o servidor 

az group create -l canadacentral -n rg-money-hubchear

az group create -l canadacentral -n rg-money-hub

# Precisa trocar o nome e localidade no qual voce tem  direito de trocas
# essa parte é usuario administrador para para os push ou quando a criação

az acr create \
 --resource-group rg-money-hub \
 --name moneyhubrm565799 \
 --sku Standard \
 --location canadacentral \
 --public-network-enabled true \
 --admin-enabled true

# Vai criar para login dele para fazer o push dele 

az acr create -n moneyhubrm565799 -g rg-money-hub --sku Standard

# Vai ser como um botao ao nosso sevidor, e é como ativar o usuario administrador e sim assim vai pode mandar o usuario

# Diferencia é uma imagem docker mas funcionando

LOGIN_SERVER=$(az acr show --name moneyhubrm565799 \
 --resource-group rg-money-hub \
 --query loginServer --output tsv)
echo ""
echo "Login Server: $LOGIN_SERVER"
echo ""

ADMIN_USERNAME=$(az acr credential show --name moneyhubrm565799 \
 --resource-group rg-money-hub \
 --query username --output tsv) && \
ADMIN_PASSWORD=$(az acr credential show --name moneyhubrm565799 \
 --resource-group rg-money-hub \
 --query passwords[0].value --output tsv) && \
echo "Username: $ADMIN_USERNAME" && echo "Password: $ADMIN_PASSWORD"


# Depois faz o login com nome e com codigo que esta presente na chave de acesso

# Essa parte aqui vai ser o login para acessar com a nome do usuario e a senha presente 

# Qual proximo passo qual deve fazer 

docker login moneyhubrm565799.azurecr.io \
  -u moneyhubrm565799 \
  -p UwIwnTULvbMOmJpAVddP8b6Onn7vLcvXgkLOUm7V2jU6J06iebvDJQQJ99CHACBsN54Eqg7NAAACAZCRHeb8

az acr login --name moneyhubrm565799 --expose-token


TOKEN=$(az acr login --name moneyhubrm565799 --expose-token --output tsv --query accessToken)

echo $TOKEN | docker login moneyhubrm565799.azurecr.io --username 00000000-0000-0000-0000-000000000000 --password-stdin



# Esse vai subir essa imagem para la so precisa pegar o nome da imagem ou nomear a imagem
docker tag mysql-dimdim moneyhubrm565799.azurecr.io/mysql-dimdim:v1

docker tag api-dimdim moneyhubrm565799.azurecr.io/api-dimdim:v1

docker tag api-transacoes moneyhubrm565799.azurecr.io/api-transacoes:v1

# Dar o push
docker push moneyhubrm565799.azurecr.io/mysql-dimdim:v1

docker push moneyhubrm565799.azurecr.io/api-dimdim:v1

docker push moneyhubrm565799.azurecr.io/api-transacoes:v1


# Lista para ver as imagems que tem dentro do container

az acr repository list --name moneyhubrm565799 --output table

Apos disso, para nao perde credito precisa remova do ambiente docker
# E isso nao remove a imagem do repositorio no registro no registro de container do azure

docker rmi moneyhubrm565799.azurecr.io/api-transacoes:v1

docker rmi moneyhubrm565799.azurecr.io/api-dimdim:v1

docker rmi moneyhubrm565799.azurecr.io/mysql-dimdim:v1

# Comandos importantes para o container
az acr repository list --name moneyhubrm565799 --output table
# Listar as tags (imagens) de um repositório
az acr repository show-tags --name moneyhubrm565799 --repository mysql-dimdim
# Mostrar manifesto
az acr manifest list-metadata --registry moneyhubrm565799 --name api-dimdim
# Limpar imagens antigas do ACR
az acr purge --name mysql-dimdim --filter 'imagem:.*' --ago 7d –untagged
# Detalhes de um repositório
az acr repository show --name moneyhubrm565799 --repository mysql-dimdim


# Parte de armazenamento - primeiro comandos
# Comando para guardar
rm=rm565799

storageAccountName="volumedimdimdata$rm" 

file_share_name="mysql-dimdim-volume"
resourceGroup="rg-money-hub"
location="canadacentral"

# Valida se o Grupo de Recursos existe e cria caso não exista
if ! az group show --name "$resourceGroup" &>/dev/null; then
  echo "Resource group '$resourceGroup' não existe. Criando..."
  az group create --name "$resourceGroup" --location "$location"
fi

# Registra o Serviço de Storage na Assinatura
az provider register --namespace Microsoft.Storage

# Cria a conta de armazenamento
if ! az storage account show --name "$storageAccountName" --resource-group "$resourceGroup" &>/dev/null; then
  az storage account create --resource-group "$resourceGroup" \
    --name "$storageAccountName" \
    --location "$location" \
    --sku Standard_LRS
else
  echo "A conta de armazenamento '$storageAccountName' já existe"
fi

## Recupera o Token da Conta de Armazenamento
connection_string=$(az storage account show-connection-string --name $storageAccountName --resource-group $resourceGroup --query connectionString --output tsv)

# Cria o compartilhamento de arquivos (Será nosso Volume do Banco de Dados)
if ! az storage share exists --name "$file_share_name" --account-name "$storageAccountName" --connection-string "$connection_string" | grep true; then
  az storage share create --name "$file_share_name" --account-name "$storageAccountName" --connection-string "$connection_string"
else
  echo "O compartilhamento de arquivos '$file_share_name' já existe"
fi


# Configuração para o dim dim do armazem

# Variaveis
rm=rm9999
resourceGroup="rg-money-hub"
location="canadacentral"
MYSQL_ROOT_PASSWORD=dimdim100206
MYSQL_DATABASE=db-dimdim
MYSQL_USER=user-dimdim-le
MYSQL_PASSWORD=dimdim100206
SPRING_DATASOURCE_URL=jdbc:mysql://mysql-dimdim:3306/db-dimdim
SPRING_DATASOURCE_USERNAME=user-dimdim
SPRING_DATASOURCE_PASSWORD=dimdim100206
CONNECTIONSTRINGS='Server=mysql-dimdim;Port=3306;Database=db-dimdim;User=user-dimdim;Password=dimdim100206;'