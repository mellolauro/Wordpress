Infraestrutura como código para WordPress Cloud Architecture na AWS (Multi Cloud AWS)

Este repositório contém infraestrutura Terraform como código para AWS Public Cloud. Ele vem com as melhores práticas de desenvolvimento de nuvem incorporadas para economizar seu tempo e dinheiro.
Aproveitar sua própria conta da AWS reduz drasticamente seus gastos mensais em comparação a pagar um caro provedor de hospedagem gerenciada (PaaS, SaaS).
Este script pode ser usado com qualquer plataforma de comércio eletrônico / Web, por exemplo. 
WordPress, WooCommerce, Drupal, Shopware 6, Shopify APP (nuvem de APP privada Custum), VueStorefront, Silyus, Oddo, ORO etc.

Porque Auto Scaling
Aumentar o número de processos PHP-FPM além do número de núcleos de processador físico não melhora o desempenho, em vez disso,
provavelmente o degradará e poderá consumir recursos desnecessariamente. A regra básica para a web é:
CPU (física) = (solicitação HTTP simultânea * http_req_duration)
Cuidado: as CPUs da Intel são virtuais e o número real de CPUs é fator = 2; 
O Graviton AWS ARM64 CPUS tem fator 1 e é melhor para processamento de solicitações simultâneas. 
CPUs da Intel têm algumas vantagens de 20-30% em alguns casos, no entanto, para magento (consultas longas e pesadas), 
os núcleos físicos são melhores. Com tráfego mais alto você precisa de mais CPUs. É a regra para páginas não armazenadas em cache.
Com Varniz / FPC é o mesmo. No entanto, o Varnish tem tempo de resposta de aproximadamente 1 ms e uma CPU de instância única 
pode retornar 1000 páginas de caches por segundo. Para evitar resultados imprevisíveis com a invalidação de cache, 
falhas, checkouts sem cache, carrinho, AJAXs, API, as melhores práticas são medir o desempenho sem FPC. 
Recursos do AWS Cloud:
        ◦ Escala automática horizontal verdadeira
        ◦ Acessível (a partir de ~ 300 $ para a região us-west-2)
        ◦ MySQL RDS escalável Gerenciado pela Amazon, failover multi az, escalonamento vertical sem tempo de inatividade
        ◦ Compatível com RDS Aurora Cluster e Aurora Serverless
        ◦ EFS - NFS elástico totalmente gerenciado para armazenamento de mídia e configuração
        ◦ CDN do CloudFront para mídia estática e servida de origens diferentes S3 ou Magento (EFS) como segunda origem
        ◦ Faça backup automático de seu código e bancos de dados (instantâneo pontual) para fácil restauração
        ◦ 99,9% de tempo de atividade, disponibilidade em várias zonas
        ◦ Alta segurança (grupos de segurança, infraestrutura privada)
        ◦ Elastic (Static) IP e usado para acesso à Internet para todas as instâncias EC2 por meio de NAT (Network Address Translation).
        ◦ Host Bastion para fornecer acesso Secure Shell (SSH) aos servidores da web Magento.
        ◦ Grupos de segurança apropriados para cada instância ou função para restringir o acesso apenas a protocolos e portas necessários.
        ◦ Sub-redes públicas privadas - gateway NAT, servidor Bastion
        ◦ Todos os servidores e banco de dados são hospedados em rede privada de forma segura
        ◦ Patches de atualização de sistema e software
        ◦ Proteção DDoS com AWS Shield
        ◦ Infraestrutura compatível com PCI
        ◦ Cluster Redis
        ◦ Serviço Amazon Elasticsearch - Elasticsearch em escala com tempo de inatividade zero com Kibana integrado
        ◦ Diferentes grupos de escalonamento de aplicativos (ASG)
        ◦ Application Load Balancer (ALB) com terminação SSL / TSL, gerenciamento de certificados SSL
        ◦ Roteamento baseado em caminho ALB, roteamento baseado em host, funções Lambda como alvos, roteamento baseado em método / cabeçalho HTTP, roteamento baseado em parâmetro de string de consulta
        ◦ Verniz Escamado ASG
        ◦ ASG de administrador / cron dedicado
        ◦ Você pode adicionar facilmente novos grupos de escalonamento automático para suas necessidades (por site da Web / para solicitações do Checkout / para API), basta copiar e colar o código
        ◦ Possibilidade de executar a mesma infraestrutura no ambiente de Produção / Staging / Dev, diferentes projetos
        ◦ Implantações automáticas de CI / CD (CodePipeline / CodeDeploy) possíveis
        ◦ Implantação AWS CodeDeploy In-place, implantação azul / verde do Git ou S3, reimplantar ou reverter
        ◦ Implantando de uma conta de desenvolvimento em uma conta de produção
        ◦ Amazon Simple Email Service (Amazon SES) - serviço de envio de e-mail baseado em nuvem. Preço $ 0,10 para 1K e-mails
        ◦ Amazon CloudWatch - carregue todas as métricas (CPU, RAM, rede) em sua conta para pesquisa, gráficos e alarmes. Os dados métricos são mantidos por 15 meses.
        ◦ Alarmes do CloudWatch que mostram uma única métrica do CloudWatch ou o resultado de uma expressão matemática com base nas métricas do CloudWatch e enviam notificações por SMS (texto) ou e-mails
        ◦ Políticas de dimensionamento simples e em etapas - escolha as métricas de dimensionamento que acionam o dimensionamento horizontal
        ◦ Dimensionamento manual para Magento Auto Scaling Group (ASG)
        ◦ AWS Command Line Interface (CLI) - ferramenta para gerenciar seus serviços AWS. Você pode controlar vários serviços da AWS a partir da linha de comando e automatizá-los por meio de scripts.
        ◦ DynamoDb para registros, índices, análises
        ◦ Lambda funciona como destino para um balanceador de carga
        ◦ Elastic Container Registry (ECR) - registro de contêiner Docker totalmente gerenciado que facilita o armazenamento, gerenciamento e implantação de imagens de contêiner Docker!
        ◦ Você pode usar o Amazon Elastic Container Service (ECS) em vez de ASG com Service Auto Scaling para ajustar a contagem desejada de contêineres em execução automaticamente.
A Infraestrutura:
A infraestrutura consiste em várias camadas (autoscaling, alb, rds, security-group, vpc) onde cada camada é configurada usando um dos módulos Terraform AWS com argumentos especificados em terraform.tfvars no diretório de camadas.
O Terraform usa isso durante a etapa de instalação do módulo do terraform init para baixar o código-fonte para um diretório no disco local para que possa ser usado por outros comandos do Terraform.
O [https://registry.terraform.io/](public Terraform registry) fornece módulos de infraestrutura para muitos recursos de infraestrutura.
O Terragrunt é usado para trabalhar com configurações do Terraform, o que permite orquestrar camadas dependentes, atualizar argumentos dinamicamente e manter as configurações. 
