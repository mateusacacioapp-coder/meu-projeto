# Gestão de Cargas — aplicação web

Aplicação web de uso individual para acompanhar cargas, clientes e pedidos, importar a carteira do BI e consultar a programação por data.

## O que já está implementado

- Login e criação de conta individual com Supabase Auth.
- Banco persistente no Supabase, com isolamento dos dados por usuário (RLS).
- Painel com cargas, pedidos, pedidos sem carga e peso total.
- Agrupamento de pedidos por número de carga e detalhamento dos clientes/pedidos vinculados.
- Cadastro e edição manual de pedidos/cargas.
- Exclusão de um registro ou de uma carga inteira (com confirmação).
- Pesquisa de cargas, clientes, cidades e pedidos; filtro por status e data.
- Importação `.xlsx` da aba `Export` (BI) ou `BASE SPI`.
- Substituição atômica apenas dos registros `origin = bi`; registros manuais ficam preservados.
- Histórico básico de importações.

## Estrutura da planilha reconhecida

A importação reconhece os cabeçalhos usados na planilha enviada, incluindo `Data a Carregar`, `Cliente`, `Cidade`, `Carga`, `Pedido`, `Quantidade`, `Peso`, `ROTA`, `Status Roteirização`, `Nm_Representante`, `Canal`, `Prioridade CS`, `Id_Cliente`, `OCT`, `Faixa OCT`, `Entrada do Pedido`, `Data Agenda Comercial`, `Tipo Pedido`, `Haver`, `Ds_CentroCusto`, `Faturamento Previsto` e `Vínculo de carga`. A coluna `Filial` da exportação do BI pode existir e é ignorada.

## Como colocar no ar

Esta pasta é o projeto-fonte. Para ter um endereço web e banco persistente, é necessário conectar um projeto Supabase e publicar os arquivos em um serviço de hospedagem estática, como Vercel ou Netlify. Não há publicação automática nem credenciais incluídas neste pacote.

### 1. Criar o projeto Supabase

1. Acesse https://supabase.com e crie um projeto.
2. Em **SQL Editor**, execute todo o conteúdo de `supabase.sql`.
3. Em **Project Settings > API**, copie a **Project URL** e a chave **anon/public**.
4. Abra `config.js` e substitua os dois valores de exemplo:

```js
export const SUPABASE_URL = 'https://SEU-PROJETO.supabase.co';
export const SUPABASE_ANON_KEY = 'SUA_CHAVE_ANON_PUBLICA';
```

A chave `anon/public` pode ser usada no navegador porque o acesso é protegido pelas políticas RLS. **Nunca coloque a chave `service_role` no frontend.**

### 2. Testar localmente

Como o projeto usa módulos JavaScript, abra a pasta por um servidor local (não pelo `file://`). Com Python instalado, no terminal dentro da pasta:

```bash
python -m http.server 8000
```

Abra `http://localhost:8000` no navegador.

### 3. Publicar

Envie os arquivos desta pasta para um repositório Git e importe o repositório na Vercel ou Netlify como site estático. Não é necessário comando de build; a pasta raiz é a publicação. Após publicar, abra a URL, crie sua conta e comece a usar.

## Como importar a carteira

1. Exporte a carteira atual do BI em `.xlsx`.
2. Entre em **Importação e relatórios** e selecione o arquivo.
3. Confira a quantidade de registros, cargas e peso identificados.
4. Confirme a substituição.

A operação substitui os dados anteriores importados do BI do usuário autenticado. Cadastros feitos manualmente não são apagados. A substituição é executada no banco em uma única transação; se houver erro, a carteira anterior é mantida.

## Observações importantes

- O sistema não está hospedado nem conectado a uma conta Supabase neste momento; isso exige que o proprietário crie o projeto e configure as chaves.
- O histórico registra data, nome do arquivo e quantidade de linhas, mas não guarda cópias completas de versões antigas da carteira.
- A aplicação carrega até 10.000 registros por consulta na tela. A rotina SQL de importação aceita até 25.000 linhas por arquivo.
- Datas são interpretadas como datas locais de calendário; confira a prévia antes de confirmar a importação.
