//m Grupo de Recursos para abrigar os recursos que serão criado
//Primeiro passo fizemos o criacao das exportamos os dockerfilhes e longo apos execultados no fizemos a criação da nossa imagem de docker
//vamos fazer um serviço de docker
//vai se feito 3 container dentro deles 


--depois fizemos um serviço apos de fazer tudo local e apos disso colocar tudo em um servidor
--agora so faz push, so jogar para cima

--Chamado de Paas - voce so criar e usar, so dar 3 comados e ja utilizar e sim é util 

--agora é automatizar o servidor 

az group create -l canadacentral -n rg-money-hubchear

az group create -l canadacentral -n rg-money-hub

--Precisa trocar o nome e localidade no qual voce tem  direito de trocas
--essa parte é usuario administrador para para os push ou quando a criação
az acr create \
 --resource-group rg-money-hub \
 --name moneyhubrm565799 \
 --sku Standard \
 --location canadacentral \
 --public-network-enabled true \
 --admin-enabled true

--Vai criar para login dele para fazer o push dele 

az acr create -n moneyhubrm565799 -g rg-money-hub --sku Standard

--Vai ser como um botao ao nosso sevidor, e é como ativar o usuario administrador e sim assim vai pode mandar o usuario

--Diferencia é uma imagem docker mas funcionando
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


--Depois faz o login com nome e com codigo que esta presente na chave de acesso

--Essa parte aqui vai ser o login para acessar com a nome do usuario e a senha presente 
az acr login --name moneyhubrm565799

--Qual proximo passo qual deve fazer 

docker login moneyhubrm565799.azurecr.io \
 -u $moneyhubrm565799 \
 -p $UwIwnTULvbMOmJpAVddP8b6Onn7vLcvXgkLOUm7V2jU6J06iebvDJQQJ99CHACBsN54Eqg7NAAACAZCRHeb8

 --Esse vai subir essa imagem para la so precisa pegar o nome da imagem ou nomear a imagem
 docker tag mysql-dimdim moneyhubrm9999.azurecr.io/mysql-dimdim:v1


--isso aqui vai enviar na nossa imagem para o nosso repositorio
 docker push moneyhubrm9999.azurecr.io/api-dimdim:v1

