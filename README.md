# osTicket Reforged

Fork controlado do osTicket para engenharia reversa e futura revitalização progressiva do frontend, preservando o core e a capacidade de acompanhar o upstream.

## Estado atual do projeto

| Item | Estado |
| --- | --- |
| Baseline | osTicket `v1.18.4`, commit `8d38b061` |
| Branch estável | `main` |
| Etapa atual | Portão D iniciado — comparação arquitetural documental |
| Observação visual | sequência estrita concluída e aprovada em revisão independente |
| Portão A — Governança | Concluído pela PR #1 |
| Portão B — Inventário | Concluído — estrutura verificada e revisada |
| Portão C — Análise profunda | Concluído — revisão cruzada aprovada |
| Portão D — Decisão arquitetural | Em andamento; nenhuma arquitetura aceita |

Ambiente de homologação instalado e ativo: XAMPP local, PHP 8.2, MariaDB,
extensões e logs preparados. A interface pública e a autenticação administrativa
foram confirmadas por observação em runtime. O snapshot oficial de referência e
o portal MkDocs permanecem no fluxo obrigatório de atualização. O portal usa
abas de contexto e categorias técnicas recolhíveis, com trilhas de leitura por
objetivo, busca global e temas claro/escuro; os caminhos canônicos dos documentos
continuam preservados.

Novas sessões devem usar o
[checkpoint canônico de retomada](docs/governance/CURRENT_STATE.md) para separar
estado verificado, decisões aceitas, itens ainda não decididos e condições
locais que precisam ser revalidadas.

A Onda 7 concluiu 25 cenários funcionais. Uma revisão de cobertura abriu a Onda
8 para demonstrar menus contextuais e ações encadeadas ainda não exercitadas.
Oito cenários complementares foram concluídos no recorte declarado, incluindo
vínculo, fusão e criação de ticket ou tarefa a partir da thread; ações em massa
ficaram mapeadas sem efeito em lote. A revisão final foi aprovada e integrada
pela PR #9.
Os cenários de vínculo/desvínculo e criação de ticket a partir de uma resposta
foram repetidos sob rollback binário e agora possuem dossiês e JSON sanitizados
no [índice de evidências comportamentais](docs/reverse-engineering/evidence/README.md).
Para inspeção humana, a homologação mantém o vínculo `903010`→`874483` e o
ticket derivado `166522`, conforme GOV-018.
Sob GOV-019, a cobertura foi reaberta para percorrer os contextos público,
cliente, equipe e Administração exclusivamente pelo frontend natural antes do
Portão D. Evidência de backend não substitui comportamento visual.
GOV-020 exige que a passagem recomece no primeiro menu e avance sem saltos;
cenários históricos de CLI, endpoint ou método direto serão repetidos pelo
navegador. Dependências locais, incluindo e-mail de homologação sem relay,
podem ser parametrizadas quando necessárias.
A Onda 9 percorreu o mapa global dos quatro contextos, configurou
e-mail POP3/SMTP local pela Administração, confirmou criação natural por coleta,
repetiu vínculo e derivação de ticket e concluiu o primeiro atendimento
realista ponta a ponta no ticket `990221`. A tarefa derivada `2` também teve seu
ciclo Aberto → Encerrado → Aberto, atualização, nota interna e formulários
secundários reproduzidos pelo frontend; BHV-022 foi repetido com edição e
restauração visual do ticket `903010`. A matriz agora explicita, cenário a
cenário, a equivalência visual ou a ausência de interface para `BHV-001` a
`BHV-034`. A passagem já
reconfirmou também a Base de Conhecimento operacional e os cenários
`BHV-016`, `BHV-020`, `BHV-021`, `BHV-023` e `BHV-024`, com restauração dos
estados temporários e preservação das evidências funcionais. A repetição também
confirmou paginação, ações em massa, vínculo/desvínculo, fusão visual sem
exclusão, edição com histórico, marcações respondido/não respondido e impressão
PDF. Cliente e agente restrito já tiveram os percursos sequenciais repetidos;
o cadastro público chegou à confirmação, ativação e login, e o SMTP visual foi
publicado e reenviado pela thread. A materialização da exportação atual
continua como divergência reproduzida: duas submissões encerraram o diálogo sem
evento de download nem arquivo novo. A Página de Login também reproduziu o
destino sem identificador e uma sobreposição vazia. Em contrapartida, a leitura
de cabeçalhos MIME e um novo `Salvar e Reenviar` foram concluídos visualmente e
preservados na homologação. Os 19 modelos de e-mail do conjunto
HTML padrão foram abertos em ordem e tiveram assunto, corpo, controles e links
de variáveis conferidos sem salvar alterações; a ajuda foi amostrada e as descrições
já haviam sido lidas na lista. Inconsistências do validador e da referência
de variáveis de tarefa ficaram registradas na matriz.
Uma matriz adicional de departamentos foi executada pelo frontend com uma
agente fictícia de Suporte: ticket não atribuído de Vendas permaneceu negado,
enquanto outro ticket de Vendas tornou-se visível pela atribuição à equipe da
agente. As fixtures foram preservadas para inspeção no ambiente de homologação.
Achados de segurança já confirmados continuam documentados, sem novos ensaios
ofensivos nesta passagem comportamental.

A [matriz de cobertura do código-fonte](docs/reverse-engineering/SOURCE_COVERAGE_MATRIX.md)
fecha o universo literal da baseline: os 2.266 caminhos estão classificados
sem sobra, com 1.215 arquivos de Laminas Mail, mPDF e PEAR separados como
dependências incorporadas. Essa garantia é estrutural e não significa execução
de toda combinação possível de configuração.

Próximas etapas:

1. obter decisão explícita sobre a direção e o primeiro recorte propostos;
2. registrar a escolha em ADR antes de planejar a prova de integração;
3. manter exclusões condicionadas a plano, backup verificável e rollback
   conforme GOV-014;
4. submeter a recomendação do Portão D à aprovação explícita, sem antecipar
   versões de Angular, PrimeNG ou detalhes de integração.

O [dossiê do Portão D](docs/reverse-engineering/ARCHITECTURE_DECISION_RECORD.md)
compara fachada PHP, serviço externo com conector e extração com dados próprios.
A [migração proposta](docs/reverse-engineering/MIGRATION_STRATEGY.md) recomenda
começar por consultas de tickets do agente. Os
[seis pareceres independentes](docs/reverse-engineering/GATE_D_REVIEW.md) não substituem
aprovação arquitetural nem autorizam implementação.

Decisão de frontend: a futura aplicação Angular com PrimeNG ficará isolada em
`frontend/`; versões e integração serão definidas após a análise correspondente.

Fontes operacionais:

- [instruções obrigatórias](AGENTS.md);
- [contexto persistente](docs/governance/PROJECT_CONTEXT.md);
- [checkpoint canônico de retomada](docs/governance/CURRENT_STATE.md);
- [controle detalhado de progresso](docs/governance/PROGRESS.md);
- [política de documentação](docs/governance/DOCUMENTATION_POLICY.md);
- [decisões de governança](docs/governance/DECISIONS.md);
- [instruções de automação](.codex/README.md);
- [plano proposto de engenharia reversa](docs/plans/active/0001-reverse-engineering.md).
- [ambiente de homologação](docs/development/HOMOLOGATION_ENVIRONMENT.md).
- [snapshot da documentação oficial](docs/upstream/osticket-docs/README.md).
- [portal da engenharia reversa](docs/index.md).

---

## Documentação original do upstream

O conteúdo abaixo é mantido para referência e compatibilidade com o projeto oficial.

osTicket
========

<a href="https://osticket.com"><img height="80px" width="80px" src="images/favicon.png"
align="left" hspace="10" vspace="6"></a>

**osTicket** is a widely-used open source support ticket system. It seamlessly
integrates inquiries created via email, phone and web-based forms into a
simple easy-to-use multi-user web interface. Manage, organize and archive
all your support requests and responses in one place while providing your
customers with accountability and responsiveness they deserve.

How osTicket works for you
--------------------------
  1. Users create tickets via your website, email, or phone
  1. Incoming tickets are saved and assigned to agents
  1. Agents help your users resolve their issues

osTicket is an attractive alternative to higher-cost and complex customer
support systems; simple, lightweight, reliable, open source, web-based and
easy to setup and use. The best part is, it's completely free.

Requirements
------------
  * HTTP server running Microsoft® IIS or Apache
  * PHP version 8.2 - 8.4 (8.4 recommended)
  * mysqli extension for PHP
  * MySQL database version 5.5 (or greater)

### Recommendations
  * ctype, fileinfo, gd, gettext, iconv, imap, intl, json, mbstring,
    Zend OPcache, phar, xml, xml-dom, and zip extensions for PHP
  * APCu module enabled and configured for PHP

Deployment
----------
osTicket now supports bleeding-edge installations. The easiest way to
install the software and track updates is to clone the public repository.
Create a folder on you web server (using whatever method makes sense for
you) and cd into it. Then clone the repository (the folder must be empty!):

    git clone https://github.com/osTicket/osTicket

And deploy the code into somewhere in your server's www root folder, for
instance

    cd osTicket
    php manage.php deploy --setup /var/www/htdocs/osticket/

Then you can configure your server if necessary to serve that folder, and
visit the page and install osTicket as usual. Go ahead and even delete
setup/ folder out of the deployment location when you’re finished. Then,
later, you can fetch updates and deploy them (from the folder where you
cloned the git repo into)

    git pull
    php manage.php deploy -v /var/www/htdocs/osticket/

Upgrading
---------
osTicket supports upgrading from 1.6-rc1 and later versions. As with any
upgrade, strongly consider a backup of your attachment files, database, and
osTicket codebase before embarking on an upgrade. Please review our [Upgrade
Guide](https://docs.osticket.com/en/latest/Getting%20Started/Upgrade%20and%20Migration.html)
or the [UPGRADING.txt file](UPGRADING.txt) for upgrade instructions.

Help
----
Visit the [Documentation](https://docs.osticket.com/) or the
[forum](https://forum.osticket.com/). And if you'd like professional help
managing your osTicket installation,
[commercial support](https://osticket.com/support/) is available.

Contributing
------------
Create your own fork of the project and use
[git-flow](https://github.com/nvie/gitflow) to create a new feature. Once
the feature is published in your fork, send a pull request to begin the
conversation of integrating your new feature into osTicket.

### Localization
[![Crowdin](https://badges.crowdin.net/osticket-official/localized.svg)](https://crowdin.com/project/osticket-official)

The interface for osTicket is now completely translatable. Language packs
are available on the [download page](https://osticket.com/download). If you
do not see your language there, join the [Crowdin](https://crowdin.com/project/osticket-official)
project and request to have your language added. Languages which reach 100%
translated are are significantly reviewed will be made available on the
osTicket download page.

The software can also be translated in place in our [JIPT site](http://jipt.i18n.osticket.com).
Once you have a Crowdin account, login and translate the software in your browser!

Localizing strings in new code requires usage of a [few rules](setup/doc/i18n.md).

License
-------
osTicket is released under the GPL2 license. See the included LICENSE.txt
file for the gory details of the General Public License.

osTicket is supported by several magical open source projects including:

  * [Font-Awesome](https://fontawesome.com/)
  * [HTMLawed](https://www.bioinformatics.org/phplabware/internal_utilities/htmLawed)
  * [jQuery dropdown](https://labs.abeautifulsite.net/jquery-dropdown/) (Project Deleted)
  * [jsTimezoneDetect](https://pellepim.bitbucket.org/jstz/)
  * [laminas-mail](https://github.com/laminas/laminas-mail)
  * [mPDF](https://github.com/mpdf/mpdf)
  * [PasswordHash](https://www.openwall.com/phpass/)
  * [PEAR](https://pear.php.net/package/PEAR)
  * [PEAR/Auth_SASL](https://pear.php.net/package/Auth_SASL)
  * [PEAR/Mail](https://pear.php.net/package/mail)
  * [PEAR/Net_SMTP](https://pear.php.net/package/Net_SMTP)
  * [PEAR/Net_Socket](https://pear.php.net/package/Net_Socket)
  * [PEAR/Serivces_JSON](https://pear.php.net/package/Services_JSON)
  * [php-gettext](https://launchpad.net/php-gettext/)
  * [phpseclib](https://phpseclib.sourceforge.net/)
  * [Spyc](https://github.com/mustangostang/spyc)
