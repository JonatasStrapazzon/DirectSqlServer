# Notas de versão

## 1.12.0

### Layout
- Janela com **abas no topo**: Restaurar, Scripts SQL, Configurações e Manutenção.
- **Scripts SQL** virou aba, com o editor embutido (Executar scripts, Salvar, Restaurar padrão); a janela separada do lápis deixou de existir.
- **Configurações** deixou de ser o card recolhível: ficam em duas colunas, e o título da aba mostra quantas opções estão ativas.
- **Manutenção** reúne o que estava no rodapé: procurar sobras, verificar atualizações e a integração com o menu do Windows.
- Via link não tem mais o botão **Ler link**: o **Restaurar** lê o índice do ZIP como primeiro passo (e deixa escolher o `.bak` se houver mais de um).
- **Autenticação do SQL Server:** em Configurações, além do Windows (padrão), dá para entrar com usuário (`sa` por padrão) e senha, com **Testar conexão**. A senha só é lembrada se você pedir, criptografada (DPAPI). O campo de instância mostra `MSSQLSERVER` quando é a padrão.
- **Notificação ao terminar** configurável: aviso do **App** (flutuante, no estilo do PRNotifier, com botão Abrir), notificação do **Windows**, **Ambas** ou **Nenhuma** (padrão), com botão **Testar**. O aviso do Windows agora aparece também com a janela à vista.
- Visual novo: cabeçalho enxuto (ícone, nome e versão), **Via link / Via arquivo** em botões, campo **Nome da base**, cartão de andamento com velocidade e tempo à direita e barra fina, botões arredondados e caixas com "v".
- **Configurações** ganhou **Verificar CRC do ZIP** e **Avisar no Windows ao terminar** (ambas ligadas por padrão); as explicações ficam em dicas ao passar o mouse.
- O botão **Cancelar** agora é próprio, ao lado de **Restaurar base**; o aviso de versão fica acima das abas.
- Se a conexão ficar muda (cabo desligado), a tela avisa em ~5 s "Sem receber dados do servidor há mm:ss…", em vez de só depois do tempo limite de 60 s.

### Integridade
- **Conferência do CRC do ZIP**, via link e via arquivo (a biblioteca do .NET não conferia). Um arquivo corrompido aborta a restauração antes do fim, remove a base parcial e diz que o ZIP está corrompido. O registro informa se o CRC foi conferido.
- Nova opção **Verificar a integridade da base depois de restaurar (DBCC CHECKDB)**, com porcentagem e tempo restante, cancelável. Roda antes dos scripts e da redução do log.

### Rede
- **A restauração via link continua depois de uma queda de rede.** O app espera, avisa ("Sem conexão com o servidor do link há 00:42…") e continua de onde parou; só desiste após 30 minutos sem conexão ou se o servidor recusar o acesso (link expirado).
- Links do **Google Drive**, **OneDrive**, **SharePoint** e **S3** (além do Dropbox) são convertidos para o endereço de download. Um link que abre uma página da web em vez do arquivo agora é identificado com uma mensagem clara.

### Sobras
- **Varredura de sobras** ao abrir e pelo link "Procurar sobras": acha bases parciais `DirectSqlRestore_…` e arquivos `restore_…` de restaurações interrompidas à força e oferece apagar. Só mexe em nomes criados pelo app, que nenhuma base usa.

### Avisos
- **Notificação do Windows** quando uma restauração termina, é cancelada ou tem problema e a janela não está à vista.
- **Atualizações:** o app verifica ao abrir e **a cada 6 horas**, e o rodapé mostra o resultado ("Nova versão X disponível" ou "Versão X · atualizada"). Continua havendo o botão **Verificar atualizações** no rodapé para checar na hora.

## 1.11.4 (instância nomeada)

### Instância nomeada do SQL Server
- Antes o app só conectava na instância **padrão** (`MSSQLSERVER`), então falhava em computadores com `SQLEXPRESS` ou outra instância nomeada, mesmo com o SSMS abrindo normalmente. Agora há o campo **Instância do SQL Server** em **Configurações**: vazio = padrão; ou digite o nome (ex.: `SQLEXPRESS`).
- O app mostra as instâncias encontradas e, se não houver instância padrão e existir uma só nomeada, usa essa automaticamente e avisa.
- Vale para restauração via arquivo, via link (o dispositivo virtual recebe o nome da instância), scripts SQL e redução do log. A conta de serviço usada nas pastas temporárias também acompanha a instância (`MSSQL$NOME`).
- A mensagem "Não foi possível conectar ao SQL Server local" agora orienta a conferir o Shared Memory e a informar a instância nomeada.

## 1.11.2

### Correções
- **Restauração via link não desiste mais em backups grandes.** O app encerrava a restauração com "O SQL Server parou de responder ao dispositivo virtual" depois de cerca de 5 minutos de silêncio, mas antes de ler o backup o SQL Server cria e zera os arquivos da base, o que leva mais que isso em bases grandes (103 GB levaram mais de 6 minutos). Agora o app espera enquanto o SQL estiver trabalhando; você ainda pode **Cancelar**.
- **Conexão travada não deixa mais a restauração parada.** Quando a conexão com o servidor do link travava sem dar erro (o app ficava em "Lendo a lista de arquivos do backup…" ou na barra parada), nada o tirava dali. Agora cada tentativa de baixar um bloco tem tempo limite (60 s, cabeçalho e corpo) e é refeita em outra conexão; as conexões do pool são renovadas com frequência.
- **Cancelar funciona mesmo com a rede travada.** Antes, o botão não conseguia interromper a espera por dados da origem.
- **Falha no meio da restauração limpa o que sobrou.** Se o `RESTORE` falhar antes de terminar, a base parcial `DirectSqlRestore_…` e os arquivos dela (às vezes mais de 100 GB) são removidos automaticamente, como já acontecia ao cancelar. Antes, ficavam na pasta de dados do SQL Server até serem apagados à mão.
- **Tempo restante repetido.** A mensagem de restauração via link mostrava "faltam ~ 12 min · faltam ~ 13 min" (duas estimativas). Agora mostra só a calculada pelos bytes.
- **Registro mais limpo.** O contador de tempo da fase de preparação ("Decorrido 00:12…") aparece só na barra de status e não é mais gravado a cada segundo no registro; a fase é registrada uma vez, com os tamanhos.

## 1.11.0 e 1.11.1

### Tela única: via link ou via arquivo
- As duas abas viraram uma tela só. O cartão **01 Origem do backup** tem dois botões de opção: **Via link** (padrão) e **Via arquivo**. A linha de entrada muda conforme a escolha (link + **Ler link**, ou arquivo + **Selecionar…**), e a mensagem abaixo do botão **Restaurar base** também.
- Via link, o nome do `.bak` dentro do ZIP é mostrado e sugerido como nome da base, igual ao que acontece com um arquivo do disco.
- As opções **Executar os scripts SQL salvos** e a nova **Reduzir o arquivo de log depois de restaurar** valem para os dois tipos de origem. As opções de exclusão de arquivos só se aplicam via arquivo (via link ficam desabilitadas).
- O botão **Cancelar** funciona nos dois tipos de origem.
- Abrir o app com um arquivo (menu do Explorer ou arrastar para a janela) seleciona **Via arquivo** automaticamente.

### Configurações recolhíveis
- As opções (exclusão de arquivos, executar scripts, reduzir o log) ficam sob um título **Configurações** que expande e recolhe. Vem recolhido por padrão e mostra quantas opções estão ativas ("Configurações · 2 opções ativas"). A janela fica mais curta.

### Reduzir o arquivo de log
- Nova opção, sempre desmarcada ao abrir o app (não é lembrada, porque muda o modelo de recuperação): depois de restaurar, muda a base para recuperação SIMPLE e reduz o log para cerca de 256 MB (até 3 passadas de `DBCC SHRINKFILE`). Mostra o tamanho antes e depois. Não use em base de produção com backups de log.

### Restauração pelo link bem mais rápida, com tempo restante
- A aba **Link (experimental)** passou a baixar **vários blocos do ZIP ao mesmo tempo** (8 conexões) e a descompactar em uma thread própria, na frente do SQL Server. No ZIP de teste do Dropbox, a leitura subiu de **14,6 MB/s para 95,9 MB/s** descompactados (cerca de 6,6 vezes mais), porque cada conexão sozinha era limitada a ~2,5 MB/s pelo servidor.
- A barra de status agora mostra a **velocidade e o tempo restante estimado** ("... · 85 MB/s · faltam ~ 3 h 05 min"). A aba de arquivo também mostra o tempo restante nas etapas com porcentagem.
- Transferência ao SQL Server em blocos de 4 MB (antes 1 MB).

### Fase de preparação visível
- Antes de ler o backup, o SQL Server cria e **zera os arquivos da base** (em um backup com 27 GB de dados e 26 GB de log, isso levou cerca de 2 minutos sem nenhuma porcentagem). O app agora mostra essa fase: "Preparando a base no SQL Server (reservando e zerando X GB de dados e Y GB de log). Decorrido mm:ss". Quando a leitura começa, a porcentagem, a velocidade e o tempo restante aparecem.
- Ao cancelar, os arquivos parciais deixados na pasta de dados do SQL Server são apagados (se nenhuma base os usa).

### Cancelar a restauração
- Novo botão **Cancelar** durante a restauração, nas duas abas (na aba de arquivo ele ocupa o lugar do botão Executar scripts enquanto a restauração roda). Pede confirmação, interrompe a extração, o download e o RESTORE no SQL Server e **remove a base parcial** criada por aquela execução. O nome final nunca aparece. Se o backup veio de um compactado, o `.bak` já extraído é mantido.

## 1.7.0

### Restaurar direto de um link (experimental)
- Nova aba **Link (experimental)**: cole o link de um ZIP e restaure sem baixar, extrair nem gravar o `.bak` em disco. O ZIP é lido pela rede (leitura parcial) e entregue ao SQL Server por um dispositivo virtual (VDI). Pico de disco: só a base restaurada.
- Precisa de administrador (o app oferece reiniciar como administrador já nesta aba). Só ZIP; restaura o primeiro backup do arquivo.

### Scripts SQL depois de restaurar
- Novo botão **Executar scripts** e um lápis para **editar** os comandos. Eles rodam na base indicada em "Nome da nova base", todos em uma única transação (se um falhar, nada é alterado), com `GO` para separar lotes.
- Nova opção **Executar os scripts SQL salvos na base restaurada**, desmarcada por padrão e lembrada após reiniciar o PC: roda os scripts automaticamente quando a restauração termina com sucesso. Se eles falharem, a base continua restaurada e só os scripts são desfeitos.
- Vem com o script padrão (ambiente de homologação e e-mails de teste). Os scripts editados ficam salvos em `%APPDATA%\DirectSqlServer\scripts.sql`, mesmo após reiniciar o PC ou atualizar o app.

## 1.6.0

### Instalador e atualização automática
- **Instalador por usuário** (`DirectSqlServer-win-Setup.exe`), sem administrador. Cria atalhos na área de trabalho e no menu Iniciar e instala o .NET 9 Desktop Runtime se faltar.
- **Atualização automática** pelos Releases do GitHub: o app verifica ao abrir e avisa no rodapé quando há versão nova. Também há o link **Verificar atualizações**.
- Desinstalar o app remove o menu do Explorer; os atalhos passam a ser gerenciados pelo instalador.

### Por baixo dos panos
- O app migrou de .NET Framework 4.8 para **.NET 9**; a conexão ao SQL Server usa `Microsoft.Data.SqlClient` (mesma conexão local por memória compartilhada e autenticação do Windows).
- ZIPs com nomes de arquivo inválidos no Windows (criados em outros sistemas) agora são listados em vez de recusados.
- Funcionalidades de restauração, compactados, preferências e tema inalteradas.

## 1.3.1

- Visual claro renovado e botão de tema claro/escuro. Última versão distribuída como ZIP (.NET Framework 4.8).
