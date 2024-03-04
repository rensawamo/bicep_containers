## サブスクリプション、 管理グループ。テナントにリソースをデプロイ

特定のリソースをさまざまなスコープにデプロイ


### Azureのリソース階層
![image](https://github.com/rensawamo/bicep_container/assets/106803080/18faa91d-5886-43ab-a829-01442ea4af7e)

組織はクラウドリソースに対するガバナンス、コンプライアンス、セキュリティ、コスト管理を強化できる

管理グループで Azureのサブスクリプションを管理
targetScope キーワードで管理
```sh
targetScope = 'managementGroup'
```

###  テンプレートを Azue にデプロイする
az sub でデプロイしている
```sh
templateFile="main.bicep"
today=$(date +"%d-%b-%Y")
deploymentName="sub-scope-"$today

az deployment sub create --name $deploymentName --location westus --template-file $templateFile
```

azure ポータルで確認すると以下のように、 サブスクリプションがデプロイされる
![image](https://github.com/rensawamo/bicep_containers/assets/106803080/28fffdec-e317-41a0-b02e-b43b24722c92)
