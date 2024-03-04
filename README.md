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




























