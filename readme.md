Este fluxo de trabalho instala a versão mais recente do Terraform CLI e configura o arquivo de configuração Terraform CLI
com um token de API para Terraform Cloud (app.terraform.io). Em eventos de solicitação pull, este fluxo de trabalho será executado
`terraform init`, `terraform fmt`, e `terraform plan`. Em eventos push para o branch main, `terraform apply` será executado.

A documentação para `hashicorp/setup-terraform` está localizado aqui:: https://github.com/hashicorp/setup-terraform

Para usar este workflow, você precisará concluir as seguintes etapas de configuração.

1. Crie um arquivo `main.tf` na raiz deste repositório com o backend `remote` e um ou mais recursos definidos.

```terraform
terraform { 
  backend "remote" { 
    organization = "example-organization"
    workspaces { 
      name = "example-workspace" 
    }
  }
}

resource "null_resource" "example" {
  triggers = {
    value = "A example resource that does nothing!"
  }
}
```

2. Gere um token de API de usuário do [Terraform Cloud](https://www.terraform.io/cloud) e armazene-o como um segredo GitHub (por exemplo, TF_API_TOKEN) neste repositório.

   ###**Documentação**:
   - https://www.terraform.io/docs/cloud/users-teams-organizations/api-tokens.html
   - https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets

3.Faça referência ao segredo do GitHub na etapa anterior usando a action do GitHub `hashicorp / setup-terraform`.
```yaml
     - name: Setup Terraform
       uses: hashicorp/setup-terraform@v1
       with:
         cli_config_credentials_token: ${{ secrets.TF_API_TOKEN }}
```
