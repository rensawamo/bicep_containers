## サブスクリプション、 管理グループ。テナントにリソースをデプロイ

特定のリソースをさまざまなスコープにデプロイ

## マネージメントグループ
組織内の複数のサブスクリプションを階層的に管理し、ポリシー、アクセス管理、コンプライアンス設定などを一元的に適用する

## 


### Azureのリソース階層

組織はクラウドリソースに対するガバナンス、コンプライアンス、セキュリティ、コスト管理を強化できる

管理グループで Azureのサブスクリプションを管理
targetScope キーワードで管理
```sh
targetScope = 'managementGroup'
```


### スコープの一般的な用途 
複数のリソースグループにリソースをデプロイすること


targetScope = 'subscription'とすることで、このBicepファイルはサブスクリプションレベルのデプロイメントを対象とする


外部モジュールを使用することで、リソースの定義を再利用し、構成をモジュール化できる


resourceGroup('ToyNetworking')を指定することで、ToyNetworkingという名前のリソースグループ内にモジュールがデプロイされる

```sh
targetScope = 'subscription'

module networkModule 'modules/network.bicep' = {
  scope: resourceGroup('ToyNetworking')
  name: 'networkModule'
```

### １つのリソースに対してスコープを指定
以下では、テナント内に管理グループをデプロイする例
```sh
resource parentManagementGroup 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: 'NonProduction'
  properties: {
    displayName: 'Non-production'
  }
}
```

### サブスクリプションを管理グループに関連ずける
Microsoft.Management/managementGroups/subscriptions というリソースの種類をデプロイする必要がある
```sh
targetScope = 'tenant'

@description('The name of the management group that should contain the subscription.')
param managementGroupName string

@description('The subscription ID to place into the management group.')
param subscriptionId string

resource managementGroup 'Microsoft.Management/managementGroups@2021-04-01' existing = {
  name: managementGroupName
}

resource subscriptionAssociation 'Microsoft.Management/managementGroups/subscriptions@2021-04-01' = {
  parent: managementGroup
  name: subscriptionId
}
```



### テンプレートをAzureにデプロイ
modules/virtualNetwork.bicep  の paramに値を代入して deplyが可能
```sh
templateFile="main.bicep"
today=$(date +"%d-%b-%Y")
deploymentName="sub-scope-"$today
virtualNetworkName="rnd-vnet-001"
virtualNetworkAddressPrefix="10.0.0.0/24"

az deployment sub create \
    --name $deploymentName \
    --location westus \
    --template-file $templateFile \
    --parameters virtualNetworkName=$virtualNetworkName \
                 virtualNetworkAddressPrefix=$virtualNetworkAddressPrefix
```


![image](https://github.com/rensawamo/bicep_containers/assets/106803080/0a791c53-0d7c-4249-a09c-ef91331521fe)


## リソースを管理グループにデプロイする
× 各サブスクリプションにポリシーの定義を行う
○ すべてのサブスクリプションを管理グループ内にふくめる

### 管理グループの作成
```sh
az account management-group create --name SecretRND --display-name "Secret R&D Projects"
```

### デプロイ
```sh
managementGroupId="SecretRND"
templateFile="main.bicep"
today=$(date +"%d-%b-%Y")
deploymentName="mg-scope-"$today

az deployment mg create --management-group-id $managementGroupId --name $deploymentName --location westus --template-file $templateFile
```
これより、マネージメントグループによって サブスクが管理される
![image](https://github.com/rensawamo/bicep_containers/assets/106803080/6e8ae20e-cef1-463b-a00a-fc36134071b1)


![image](https://github.com/rensawamo/bicep_containers/assets/106803080/2ea3ecfc-9e20-4d07-a6b3-8828e750351c)






