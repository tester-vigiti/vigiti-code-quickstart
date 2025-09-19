# Guia Rápido de Instalação - VIGITI Code

Bem-vindo ao VIGITI Code! Em menos de 5 minutos, você terá análises de compliance e segurança automatizadas no seu projeto. Siga estes 3 passos simples.

### Passo 1: Configure seu Token Secreto

O VIGITI Code precisa de um token de acesso para se comunicar com nosso serviço de forma segura. Você deve guardá-lo nos "Secrets" do seu repositório no GitHub, onde ele fica criptografado e protegido.

1.  No seu repositório do GitHub, vá para **Settings** > **Secrets and variables** > **Actions**.
2.  Clique no botão **New repository secret**.
3.  No campo `Name`, digite exatamente `VIGITI_TOKEN`.
4.  No campo `Secret`, cole o token único que enviamos para você por e-mail.
5.  Clique em **Add secret**.

![Exemplo de como adicionar um secret no GitHub](https://i.imgur.com/7gB9Y7y.png)

### Passo 2: Crie o Arquivo de Workflow

Agora, vamos instruir o GitHub a executar a análise do VIGITI Code automaticamente.

1.  No seu repositório, navegue ou crie a seguinte estrutura de pastas: `.github/workflows/`.
2.  Dentro da pasta `workflows`, crie um novo arquivo chamado `vigiti-code-scan.yml`.
3.  Cole o conteúdo abaixo, exatamente como está, dentro do arquivo `vigiti-code-scan.yml`:

    ```yaml
    # Nome do fluxo de trabalho que aparecerá na aba "Actions" do GitHub.
    name: 'VIGITI Code Analysis'
    
    # Define que esta análise deve rodar em cada push ou pull request para a branch main.
    on:
      push:
        branches: [main]
      pull_request:
        branches: [main]
    
    # Permissões necessárias para a Action funcionar corretamente.
    permissions:
      security-events: write
      contents: read
    
    jobs:
      vigiti-code-analysis:
        name: VIGITI Code Analysis
        runs-on: ubuntu-latest
    
        steps:
          # 1. Baixa o código do seu projeto para a máquina de análise.
          - name: 'Checkout code'
            uses: actions/checkout@v4
    
          # 2. Executa a análise de segurança e compliance com o VIGITI Code.
          - name: 'Run VIGITI Code Analysis'
            # Esta é a Action oficial do VIGITI Code.
            uses: vigiti-code/vigiti-action@v1 # <-- ATENÇÃO: Este caminho será o oficial da sua Action.
            with:
              # Lê o token de forma segura dos segredos que você configurou no Passo 1.
              token: ${{ secrets.VIGITI_TOKEN }}
    
              # Define que o workflow irá falhar se encontrar violações de nível 'high' ou 'critical'.
              fail-on: 'high'
    
              # Define as linguagens a serem analisadas no projeto.
              languages: 'js,ts,python,java'
    
          # 3. Envia os resultados para a aba "Security" do seu repositório.
          - name: 'Upload SARIF to GitHub Security'
            if: always() # Garante que os resultados sejam enviados mesmo que o passo anterior falhe.
            uses: github/codeql-action/upload-sarif@v3
            with:
              sarif_file: 'out/results.sarif'
    ```

### Passo 3: Pronto! Verifique os Resultados

É isso! A configuração está completa.

A partir de agora, a cada `push` ou `pull request` nas branches configuradas, a análise do VIGITI Code será executada.

Você poderá ver os resultados em dois lugares:
* Na aba **Actions** do seu repositório, para ver os logs completos da execução.
* Na aba **Security** > **Code scanning**, para ver as vulnerabilidades encontradas de forma interativa e detalhada.

---
**Dúvidas?**

Se encontrar qualquer dificuldade, basta responder ao e-mail de boas-vindas que te enviamos. Estamos aqui para ajudar!
