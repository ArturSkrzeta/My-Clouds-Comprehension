AWS - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

serverless
- usluga do ktoej wrzucamy kod biznesowy a ona zajmuje sie provisioningihiem serwera, odpaleniem kodu i placimy wlacznie za czas wykonania kodu
- nie placimy za idle
- my tak naprawde uruchamiamy w chmurze uslusge badz aplikacje, a nie wirtualna maszyne
- komunikacja od nas idzie do regionu a nie do konkretnego az  -  on jest automatycznie dystrybuowany

+ wszystko dzieje sie w VPC -w nich sa private subnets (partitions of vpc network separated from others for putting db or ec2 instances) - w prywatnych podsieciach, ktore sa nieosiagalne z internetu
- instances in public subnet has unique public IP address, instances in private subnet has unique private IP address
- internet gateway (IGW) - allows communication between VPS and internet - in and out, 1  IGW per 1 VPC,
- every AWS instance has a private IP address but in order to communicate with the inernet it has to get public IP address or elastic IP address
- IGW translates private ip address to public when request out to internet and when the response come IGW translates public IP adrress into the private one
- NAT gateway allows instances in the private subnet to communicate with internet outsite
- VPC comes with private IP address range called CIDR
- between AZ, there is a local router so that ec2 servers in different AZs can communicate with each other
- VPS comes with Route Table which maps IP with location - according to that tbale the traffic is routed
- 1 AZ can have multiple public subnets, however the same subnet can exist only within one AZ

+ globalna intrastruktura aws sklada sie z regionow, a regiony z availability zones (AZ) - one or more separate data centers, i edge locations (the closests place for an user to get connected to)
+ ec2 ma cos obliczac bo to jest compute, a nie serwowac statyczny content - od tego jest storage (s3), ktory ten content przetrzymuje

+ S3 - key value storage + metadane = key sciezka do pliku, value to conent pliku
- no server to maintain
- posaida tryb static hostingu - hostowanie strony bez serwera - lepszy performance
- container of objects (content) within a certain namespace
- bucket needs to be globally unique
- 5TB max size
- retrieving content form s3: by url (works only if bucket is publically exposed - by default it's unabled), programatically with lambda s3Client.get_object(...) - python boto3 sdk, thorugh the AWS management console
- s3 storage classes: standard, intelligent, infrequent access, glacier for archiving -  costs get lower but availabilit and latency get bigger - lifecycle rules for data movement automating
- security: public access is blocked by default, data protection (s3 bucket ensures encryption in transit and at rest), restrective access (AWS IAM controls the access), auditing (access logs, action based logs, alarms),


+ amazon glacier - to jest archiwum - trzeba wyslac request zeby dostac dane
+ AWS lambda usluga serverless - utrzymujemy w chmurze srodowisko uruchomieniowe w roznych jezykach - runtime do 5 minut
+ Route53 maping domain name into a specific service like load blalncer or S3
+ Amazon RDS - silnik bazy danych postawiony na instancji EC2 - scheduled resotre at point of time, automated snapshots - master-slave
+ AWS IAM - instancja EC2 jako maszyna wirtualna (instancja linuxa) - 1 dostep sieciowy przez ssh - drugi rodzaj dostepu to kontakt z API AWS, kazdy request do Restowego api aws'a musi byc podpisany, nie ma anonimowych requestow do aws'a, do podpisywania requesta slza AWS secret access key oraz AWS access key ID, te klucze sa stworzone dla danego usera,
+ Load balancer - rozklada ruch pomiedzy dostepne AZ w danym regionie
+ AWS Athena - SQL interpreter on s3 files making usage of Hadoop infrastructure. Mozliwa konwersja danych csv na parquet (fomat kolumnowy) - wtedy athena dziala duzo szybciej gdyz ilosc danch do przeanalizaowania jest duzo mniejsza.
- rozwiazanie serverless - placimy kiedy korzystamy - gdybysmy postawili DW, to placimy nawet wtedy kiedy nikt nie korzysta
- athena pod spodem ma rozwiazania open-sourcowe - hive i presto - aplikacje z ekosystemu hadoop - analiza rozproszona (multiple servers)
- athena to jest interpreator sql'a na danych, ktore sa w S3 - ona nie kopuje nigdzie danych
- client sql / applikacja - > driver atheny - > zapytywanie danych w S3
- w athenie database to jest wirtualna przestrzen ktora skupia metadane odnosnie danych z s3 - w tej database mozemy stworzyc rozne tabele dla danego objektu z danymi w s3
- aws glue - data catalog do identyfikacji typy danych kolumny i etl - crawler moze zapisac nam data catalog w bazie atheny
- w athenie musimy pamietac, zeby zaznaczyc output s3 buckets
- athena zawsze siega do S3 przy kazdym query - jak pojawi sie aktualizacja pliku juz zkatalogowanego, to ja bierze
- optymalizacja kosztowa - zipowanie gzip kopresja plikow w S3 do mniejszego rozmiaru - kwerenda skanuje plik - tak dlugo jak skanuje tyle zaplacimy
- optmalizajcja kosztowa - zmiaana formatu danych z csv na parquet - columnar storage - athena sama potrafi sfkonwertowac plik - create - create table from query - outpit w innym s3 bucket - kowersja kosztuje ale to sie zwraca w szybkosci skanowania
- 1TB przeskanowania kosztuje 5 dolarow
- format parquet ma juz jakies metadane dlatego jak robie np count all to skanuje zero rekordow bo siega juz do tych metadanych - parequt zapisuje kolumanmi na macierzach
- formaty parquet sa nieczytelne dla czlowieka - krzaki jak sobie to otworze
- optymalizacja -> partycjonownaie - podfoldery - zamiast wrzucanie wszystkiego do jednego bucketu - crawler zobaczy podfoldery z liczami jako rok miesiac dzien - on potraktue to jako patrycje czyli dodatkowe atrybuty rekordkow danych
- craweler widzac podfoldery bierze ich nazwy i tworzy jako atrybuty - pozniej odpytywanie danych z klauzula WHERE nazwa_partycji = '01'
- nie ma sensu wrzucac terabajtowe logi do DW


oszczdnosc:
- serverlessowe rozwiazania zamiast stawiania serwerow lub hurtowni danych
- kompresja danych lub kolumnowe straoge
- partycjonowanie w s3 zamiast wrzucanie wszystkiego w jeden folder



+ dynamodb nierelacyjna - baza serverlessowa - musimy zaznaczyc on demand

+ we use tags in order to organize/track/control access for a role.
+ route 53  - tlumaczenie domen www na numeryczne adresy IP
+ kinesis firehose - ingesting large volumens of data batchin data into chunks into s3 - firhose automatically sclaes based on the amount of data streamed -> we can trigger an envent to invoke lambda function
+ athena - when we don't want to pay extra mone for maintaining and provisioning db and we don;t want to keep the data in the tables but we can query S3 bucket data with sql statements.
+ Amazon QuickSight - BI, data visualization
- Machine Learning insights to the analysis additionally
- dashboards can be embedded in a different application
+ AWS Lake Formation
-  building data pipelines: data > s3 > aws glue crawler > aws glue data catalog > aws lake formaton > athena, emr, redshift
- zarzadza bezpieczenstwem dostepu do danych w S3
- blueprinty do prac etl

+ aurora - baza relacyjna kompatybilna z mysql i postgresql - b dobrze sie skaluje
- aurora rds when consistent traffic
- aurora rds serverless when there is no consistent traffic

+ redshift
- baza OLAP do BI
- duzy performance -> w jednej AZ
- columnar data storage: ma szybko odpowiadac na zapytania agregacyjne: sum, count, average, bulk data loading - wszystkie wartosci danego atrybuty sa w jednym bloku obok siebie
- mozliwie unikac deletow
- IAM roles pozwala na integorwanie z innymi serwisami
- redshift query editor

+ Elasticsearch:
-

Amazon ECS - elastic container service:
+ container orchestration tool - managing, scaling and deploying containers
+ ECS cluster contains services to manage lifecycle of containers - controllig Virtual machines (ec2 instances that hosts containers are connected to ECS cluster) that run container to be more specific
+ on EC2 instance there is a contianer runtime and ECS agent for communication between ecs processes and each individual ec2 instances
+ z kontenera moge stworzyc funkcje lambda

AWS Fargate
+ as long as the ECS is a container orchestration tool, AWS Fargate is the alternative to ec2 instances - instead of creating a provisioning ec2 instances and connecting them to ECS cluster
+ with AWS Fargate we don't need to provison any virtual machine, it spins up VMs for us
+ serverless way to launch containers
+ it analyzes a conatianer on how much resources does it need - how much cpu, how much ram, how much storage it needs to be deployed to run - after that aws fatgate provisions a server itself automatically
+ with that solution we don't need to provison any ec2 beforehand - we pay only for how long a container runs, for cpu and memory that was requested for a conatiner
+ containers that were run with fargate are also accessible from ECS to manage
+ usługa fargate - usluga serverless z klastrami do budowania serwisow opartych o dockera. bez utrzymywania ec2 - platnosc za pamiec i vCPU przydzielona dla kontenerow.
+ both ECS and Fargate are free to be integrated with whole AWS ecosys


EKS - elastic kubernetes service:
+ while ECS is specific to AWS and cannot be migrated to another platform, EKS is not specific to AWS as it is an independet tool that is easy to migrated
+ Migration of EKS may be difficult when integrated with different AWS servicess
+ we use ECS instead of EKS when we have less complex applications
+ we start with creating EKS cluster - control plane that consists with kubernetes masternodes
+ worker nodes run the containers - we need to create ec2 instances making so called compute fleet and connecting the to EKS cluster: compute fleet of ec2 instances connected with masternodes group
+ we can connect a EKS cluster using cubectl command and start deploying containers inside that cluster
+ communication betweencontrol plane (master nodes) and compute fleet is possible thanks to K8S Worker Processes
+ we can use EKS with:
! with ec2 instances - aws zarzadza control plane a user zarzadza serwerami - ec2 istnieje i trzeba za nie placic nawet jak sie nie korzysta
! with managed nodegroups - grouping worker nodes - managed serwers by aws
! integrated with serverless Farget we have compute fleet and control plane fully-managed by AWS
+ there is also etcs within control plane which is a database or a cluster
+ multiple AZs, rozne komplesy data center - duza dostepnosc i niezawodnosc
+ do komunikacji z k8s przez api kubernetesowe - Kubectl
+ eksctl -> eksctl create cluster

ECR - elastic contianer registry - repozytorium do trzymania docker image'ow i stamtad uruchomic
+ store, manage and deploy docker images
+ alternative to dockerhub
+ when new image pushed to a registry automatcally pushes it to cluster

+ Elasticache:
- caching solution
- redis

+ documentdb
- easier to user

+ dynamodb
- easier to scale

+ Amazon cloudfront - content delivery network:
- usluga globalnego cdn
- content jest cashowany blizej uzytkownika - czas reakcji aplikacji jest duzo szybszy
- redukuje ilość requestów, które trafiają do serwerów - nie odpytujemy cały czas o te same źrodła serwera głównego, bo są już zapisane po stronie edge location
- origin server can be oth on-premise or in the clodu

+ AWS lambda:
- layers for installing additional libraries

+ uslugi zarzadzane -  stawianie db amazon aurora - management, maintenance, autoscaling, monitoring, security patching is on AWS side, automated backups,
+ uslugi niezarzadzane - stawiam ec2, instaluje sobie silnik db na os i sie loguje do ec2 kluczem ssh
+ mozemy wiec zainstalowac i skonfigurowac baze danych postawiona na EC2 albo skorzystac z AWS managed database service

+ sa trzy opcje do interakcji z AWS
- Aws management console
- CLI command line interface - interfejs wiersza polecen
- zestaw do tworzenia oprogramowania AWS (SDK) - sdk for python is called boto3 - tworzenie, konfigurowanie i zarzadzanie uslugami AWS

+ IAM:
- dokumenty polityk moga byc dolaczone do uzytkownika, grupy lub roli
- uztykownik/rola podczas uwierzytelniania przez AWS otrzymuje uprawnieniaw w oparciu o dokumnet polityki
- uzytkownik jest uwierzytelniany w oparciu o nawe uzytkownika/haslo lub id klucza/secret key
- authentication: rootuser (all rights to AWS), IAM user (tozsamosc definiowana wew AWS z odp uprawnieniami), externally authenticated user (Active Directory User),
  IAM role (tozsamosc ktora moze byc przypisana do wiecej niz jednego usera/serwisow/aplikacji do wykonania operacji assume + rola nie posiada credentailow, generowane sa tymczasowe, sesyjne klucze),
- group - many IAM users
- dostepy nadawane w IAM policies,
- when creating a new user we can decide about access type: when programmatic access then access key secret, access key
- we can assign policy to an user from a gorup, from exisiting user or AWS plicy or I can create it from scratch
- mozemy nadac uparwnienia w polityce do stopowanie EC2 wtedy gdy okreslony tag ec2 instancji jest rozny od np 'Production'




+ application load balancer
- dystrybutor ruchu, ktory skaluje sie na wskors kilku obszarow data centers i routuje requesty od uzytkownikow do zdrowych instancji serwerow ec2 - helf checki - ilosc prob w jednostce czasu przekracza limit to load balancer nie wysyla requestow do tego servera
- wszyscy userzey lacza sie z jednym endpointerm - url'em load balancera i on bedzie bedzie dystrybuowal tych uzytkownikow pomiedzy wszystkie instancje serwerow

+ skalowanie:
- vertical scaling: pionowe - zwiekszanie pojemnosci biezacego serwera
- horizontal scaling: horyzontalne -zwiekszenie ilosci instancji zasobow
- we can creat Auto scaling group for EC2 instances

+ Amazon Machine Image AMI:
- os dla EC2

+ data flow: komunikaty -> amazon kinesis data streams (like apache kafka) -> amazon kinesis data analytics (analizowanie danych na biezaco - zapytania pseduo-sqlowe - transofrmacje - agregaty - joins) ->  amazon kinesis delivery streams / firehose -> s3 / sql / BI /

+ security group:
- firewall dla serwera
- ec2 > network and secuirty

+ CloudFormation Designer:
- cloud formation allows to programmatically deploy repeatable infrastructure
- drag and drop designing
- converting to YAML or JSON

+ CloudFormation:
- busienss recovery - odtworzenie srodowiska produkcyjnego cloud formation
- możliwośc i łatwość cofania zmian
- version controlling - sledzenie zmian- wyeliminowanie recznych zmian w srodowisku chmurowy - zmiany do pliku - commitowanie do repo
- CI/CD piepline
- cdk - cloud development kit - definiowanie infeastruktry za pomocom innych jezykow programowania
- forms a stack of AWS resources


+ Data Protection services:
- Amazon macie
- Amazon key management service
- Amazon CloudHSM
- AWS Certificate Manager
- AWS Secrets Managers

+ Amazon EMR Elastic Map Reduce:
- firstly creating s3 bucket for storage and ec2 for compute
- fully managed data lake service
- compute clusters scalable on demand
- it allows for multiple clusters to access the same data at once - high performance
- secondly creating a cluster with a specific application: Hadoop with Hive or different
- the hive script can be put in the s3 bucket as well - script uses HiveQl which is SQL-like scripting language
- processing the data is possible by running HIVE script as step (step is a unit of work that contains one or more Hadoop jobs).
- hive jobs write a lot of data to hdfs
- you lose the hdfs dat aonce emr clustrers are terminated data in hdfs exist only during runtime we need to push the processed data back in the s3 into another bucket
- parquet format best for reading from - pivoting and groupung data by column + additional metadata that helps iterate through a file or event enables to skip some unnecessary columns
- there are nodes: master node (managing cluster), core nodes (computing node and data node)
- there is EMR studio

step functions:
https://www.youtube.com/watch?v=s0XFX3WHg0w&ab_channel=BeABetterDev
- workflow processing

most important services
https://www.youtube.com/watch?v=B08iQQhXG1Y&t=11s&ab_channel=BeABetterDev


SNS vs SQS (Simple queuing service)
https://www.youtube.com/watch?v=mXk0MNjlO7A&ab_channel=BeABetterDev


The AWS SDK (software development kit) for browser-based development allows developers to access AWS from JavaScript code running directly in the browser. ... The SDK includes JavaScript objects for connection to important AWS components such as Amazon S3, Amazon SQS, Amazon SNS and DynamoDB.





Amazon OpenSearch Service (successor to Amazon Elasticsearch Service)

There are four big NoSQL types: key-value store, document store, column-oriented database, and graph database.



SDK Software Development Kit

EC2 - wirtualne servery - moc obliczeniowa w chmurze - computing resources - wirtualna maszyna
dostawca zarzadza platforma uruchomieniowa w serverless

rozwiazania integracyjne (wazne w mikrouslugach):
+ api gateway

+ sns - usluga notyfikacyjna - topic notyfikacyjny i subskrybenci - wiadomosc do sns jest rozsylana do subskrybentow
- systemy wydawcow moga wysylac wiadomosci do subskrybentow - kolejek SQS, AWS lambda

+ sqs
- kolejka - tam sa utrzymywane poki jeden z kosumentow nie zapyta sie o wiadomosc -  asynchroniczna komunikacja pom uslugami

+ step functions - procesy zlozone z krokow oparte na funkcjach
+ event bridge
- planowanie dziala na zdarzenia w aws, schedulowanie powaidomienia o zdarzeniu w sns
- bezserwerowa szyna zdarzen (event bus) - budowanie aplikacji kierowanej zdarzeniami
+ appsync - graphql api

bazy:
+ relational: aurora, rds
- aurora - kompatybilna z mysql i postgreslq
+ key-value: dynamodb
+ document: documebtdb
+ in-memory: elasticache - magazyn danych in-memory w czasie rzeczywistym: buforowanie, przechpwywanie sesji
+ graph: neptune

redshift:
+ hurotownia daych w chmurze
+ uzycie sql

+ lambda funcion to pre-process data etle between S3 buckets
ses - simple email service
emr - big data


AWS dla devow:
+ aws codebuid - continuos integration - testy i buduje pakiety aplikacji do wdrozenia
+ aws codecommit - system kontroli wersji -  hostuje prywatne repo git.
+ aws codedeploy - wdrazanie apikacji do uslug obliczeniowych / serwerow
+ aws codepipeline - automatyzuje fazy budowania, testowania i wdrazania gdy nastepuje zmiana kodu

AWS management and monitoring
+ aws auto scaling; monitoruje aplikacje i dostosowuje pojemnosc, zeby utrzymac wydajnosc

API gateway:
+ to zasob/oprogramowanie, ktore przekierowuje ruch pyrzchodzacy na bazie zapythan HTTP do zdefiniowanych serwisow lub mikroserwisow
+ api dzialaja jako 'drzwi wejsciowe' dla aplikacji w celu uzyskania dostepu do danych, logiki biznesowej lub funkcjonalnosci z uslug backendowych
+ restful - styl arhcitektury oprogramowania opierajacy sie o zbior wczesniej okreslonych regul opisjacyhcn jak zdeginiowane sa zasoby i umozliwiajacy doste do nich
+ websocketapi - dwukierunkowa komunikacja aplikacje w czasie rzeczywistym



Data Lake:
+ scentralizowane repo dla danych ustrukturyzowanych i nieustrukturyzowanych w dowolnej skali
- repozytorium - miejsce do uporzadkowanego przechowywania danych, ktore przezaczne jest do udostepniania
- scentralizowanie - pod wzgledem logicznym, dane z wielu systemow
- skala: chcemy miec wirtualnie nieskonczona przestrzen (co jest mozliwe dzieki chmurze), glowne zalozenia data lake jest to ze dane beda nieskonczenie przyrastac
+ data lake jest to cos wiecej niz tylko przechowywanie danych, jest to centrum dancy do przeprowadania na nich dzialan (w przeciwienstwie do gita) takich jak ml, data profiling, data discovery
+komponenty DL:
- strucutred or unstructured data ingetsion (both batching and streaming) - DL udostepnia sposoby na zaladowanie danych z innych systemow, bez potrzeby budowaania przez inne systemy rozwiazan na wyslanie danych
- ulimited scale and storage with data protection - dane przyrastaja w czasie rzeczywistym - nadawanie uprawienie granularnie, zarowno do calego jeziora jak i do pojedynczych plikow
- catalog and indexes for data - bez katalogowania nie ma jeziora, zrozumienie zawartosci jeziora jest kluczowe, bo jest tam wszystko - dlatego sa katalogowanie i indeksowanie danych - zle katalogowanie lub jego brak prowadzi do powstania bagna (data swamp) zamiast jeziora
- data analysis without data movement - w data lake analityke przeprowadzamy w miejscu, gdzie dane sie juz znajduja -nie trzeba przenoscic do systemu analitycznego
- integration with data analytics and ml tools.
+ benefits;
- elasticity: my tylko konfigurujemy srodowisko, dostarczamy dane i analizujemy je
- serverless - placimy tylko za storage  i moc obliczeniowa podaczas analityki
- brak infrastruktury do utrzymyania i rozszerzania - im wiekszy system tym wieksze ryzyko ze jakis element padnie - macierz, serwer, dysk, bibiloteka
- managed service - patching, scaling (dodawanie serwerow, dyskow), aktualizacja
- zarzadza dostepem do metadanych w katalogu i podleglych lokalizacji w amazon s3
- korzystamy z audytów i certyfikacji bezpieczenstwa dostawcy chmurowego
- blueprints for etl jobs
+ dw vs dl
- zrodlo danych: relacyjne bazy systemow tranzakcyjnych / operacyjnych baz danych || dowolny system o dowolnej charakterysytce
- schemat danych: schema on write - dane do dw juz musza miec okreslona strukture || schema on read - dopiero przy odczycie musimy sobie ten schemat utowrzyc (dzieki temu mamy ta roznorodnosc)
- dane: dane sa juz oczyszczone gdyz tego wymaga schemat i podlaczony system analityczny || dane oczyszczone lub takie jak pierwotnie sa z systemu albo tego jakie mam wymagania przy przetwarzazniu
- odbiorcy: analitycy || wszysycy: developerzy, analitycy, data scientists
- dzialania: BI, wizualizacje, raporty || analityka, ml, predykcje, profilowanie, data discovery
+ data swamp:
- dane w jeziorze staja sie nieosigalne
- katalogowanie danych po dacie, godzinie, zrodlach
- zarzadzanie dostepami IAM
- okreslenie i wdrozenie biznesowych wymagan zeby zapobiegac
+ usluga AWS LAKE FORMATION daje katalogowanie, zarzadzanie dostepami IAM,  dostepnosc infrastruktury, okreslenie wymagan i zbudowanie rozwiazanie:
1. data ingestion (EMR or serverless AWS Glue)
2. data cataloging manually or with crawlers from AWS Glue showing data structure
3. data access for users
4. integration with S3 as objects storage, Amazon Athena, Redshift, EMR
+workflow:
I. kinesis fiehose
- w kinesis otrzymujemy url pod ktory dane sa wstrzykiwane, i te dane sa dalej przekazywane do innych uslug
- utowrzenie strumienia - transforming records with AWS lambda possible: stream > lambda > storage
- cnverting to columnar format like Apach Parquet or Apache ORC - zopytmalizaowany format pod zapytania z Atheny
- saving to S3 - it gives prefix to prevent from duplicates objects
- object buffer - 5 mb or 3000 second
II. Lake Formation
1. s3 bucket / glacier afterwards - jak nie znamy wzorcow dostepowych - nie wiemy jak czesto dane beda pobierane z S3 - uzywamy intelligien tiering - same zmienia klasy storegaowe zeby optymalizaowac koszty
2. glue crawler
3 data catalog i schematy
4. athena serverless servie for sql - thanks  to data catalog can query the data
5 quicksight dla BI (athena as a data source) -
configuring Lake Formation:
1. S3 bucket for raw data
2. database for metadata - tworzymy tabele - jedna tabela jeden schemat danych - tworzy aws glue crawler
3. giving permissions - daine dostepu do lake formation crawlerowi - zeby dal schemat do tabeli, nadanie sobie uprawnienia do bazy ktora chcemy przegladac w lake fomration poprzez athena, nadanie rowniez uprawnien dla QuickSight
4. optymalizacja - parquet, kompresja, partycjonwanie na podstawie dat. przeszukiwanie danych tylko w jednej partycji - pomaga rowniez zrozumiec strukture danych

- w athena we can create table from query i stworzyc format parquet - nowa tablea jest stowrzona w lake formation bo byla stowrzona na bazie tej ktora tez jest w lake formation
- query in place
- schema on read
- decoupled storage and compute


AWS CloudWatch
+ monitoruje zasoby AWS lub aplikacje, ktore na tych zasobach dzialaja
+ standard and customized metrics
+ automated action based on the rules: for example terminating ec2 and running a new one

EBS - elastic block storage
+ local to an individual ec2 instance storage - accessing the data really quickly
+ data organized in unique block like in hard-disk -
+ lowest latency access to data
+ for relational and nosql db
+ darta warehouse etl

EFS - elastic file system
+ storage shared between multiple ec2 instances sharing the same data at the same time
+ heirarchical structure
+ should be mounted on ec2 or on-premises servers
+ for Big dataand analytics - a lot of transformation on the same data by multiple servers
+ for container storage

S3
+ stand alone storage that can be accessed either from internet or AWS vpc,
+ flat structure even though it might seem like there are subfolders,
+ API access with HTTP/HTTPS from ec2 instances,
+ for media, log files, backup, archives
+ content management and version contolling + lifecycle management - przesuwanie plikow do archiwum
+ przechowywanie danych w S3 w chmurze jes duzo tansze od stawiania hurtowni danych - mozna przeanalizaowac ad-hoc bez przemieszaczania danych

Amazon cognito:
+ systema autentykacji, autoryzajcę i zarzadzanie użytkownikami aplikacji
+ rejestracja uzytkownikow

Tagowanie:
- np po mazwie teamy widac w jakim obszarze sa najwieksze koszto
- w s3: granularly controlling access, analuzing usage, managing lifecycle policies, replicating objects

AWS billing console:
-
-

AWS Glue:
- there is AWS Glue Studio for visually building etl

Managed Apache Airflow:
- runs the actual open source Apache Airflow software in the environment Amazon manages for you,
- first step is to create the environment with a specific version of Apache Airflow,
- major version upgrades can be applied on demand and any possible rollbacks can be also supported,
- airflow workflows are represented as collection of tasks organized by their relationship and dependencies - DAG - Directed Acyclic Graph,
- we can write DAGs using Python and store DAG workflows in S3,
- we can put any plugins and libraries in requirements.txt while environment creating,
- amazon ECS on Amazaon Fargate + celery executor for running tasks in parallel per containers
- how to set up: 1. creating an MWAA environment, copy DAG and plugins to S3, access the airflow UI
- apache airflow comes with many built-in sensors to check s3 and operators to interact with AWS services executing SQL queries against CSV files
- managed workflows integrates with Amazon CloudWatch for logging and monitoring
- setting up enviroment:
: selecting VPS in which enviroment will be installed
: each environment requires to select pricate subnets in two different AZs from the VPC
: choosing how we want to access Airflow UI = 1. private network option- private endpoint accessible only from inside your VPC, 2. publicly accessible endpoint - both are authorized and authenticated with AWS IAM
: setting up max number of workers to scale up
: enviorment ensures encryption
: ensures also monitoring  in cloudwatch metrics
: workers assume IAM execution role to access other AWS services from your tasks

AWS Secrets Manager:
+ sekret jest domyslnie enkyptowany przy uzyciu AWS KMS - AWS Secrects Manaer creates a default service encryption key
+ wybieramy baze danych, do ktorej sekret bedzie przypisany
+ we can replicate secrets to another regions
+ automatyczna rotacja hasel - zmienianie hasel - podpiecie lambdy ona bedzie automatycznie rotowac sekret


Past:
Tradiditonal 3-tier application architecture:
1. web servers - presenation layer
2. application server - business logic
3. database servers - data layer

Now:
Podejscie cloud uslugowe: event driven processing
logika biznesowa to polaczone ze soba AWS mikroserwisy: kontenery, serverless, fuction as a service
kazda mikrousluga ma swoja wlasna baze danych

computing ewolution:
1. physical machines, on-premise, requires guess planning, long deployments
2. virtual machines, hardware independence, faster provisioning, better scaling, reduced maintenance.
3. containerization, platform independence, consistent runtime environment, easier and faster deployment, isolation and sandboxing, fast deployment
4. serverless, continues scaling, fault tolerance built-in, pay for value, zero maintenance


Używając AWS z pewnością korzystasz z AWS IAM, serwisu do zarządzania dostępem do usług i zasobów. Warto robić to dobrze. Poniżej 7 dobrych praktyk:
1. Nigdy nie korzystaj z konta root do codziennej pracy. Zamiast tego używaj kont z najmniejszą liczbą uprawnień wymaganą do pracy.
2. Regularnie monitoruj swoje IAM policies i usuwaj uprawnienia do serwisów z których już nie korzystasz. Regularnie zmieniaj hasło i rotuj klucze dostępowe.
3. Używaj mocnych haseł i włącz wieloskładnikową autentykację.
4. Na maszynach EC2 w celu nadania uprawnień do innych serwisów AWS używaj IAM roles.
5. Nigdy nie dziel się kluczami dostępowymi (access key), ani nie zapisuj ich w repozytorium.
6. Podczas tworzenia IAM roles, w celu zwiększenia bezpieczeństwa używaj dodatkowych warunków np. zakres adresów IP.
7. Monitoruj aktywność na swoim koncie za pomocą AWS CloudTrail i przeglądaj logi CloudWatch oraz Amazon S3. Dane historyczne o swoich IAM rolach, grupach i polisach znajdziesz w AWS Config. #aws

