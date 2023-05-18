# Radiant: un Système d'Actifs Numériques Pair-à-Pair

**11 août 2022**
**radiantblockchain.org**

**Résumé :** Le réseau Radiant est un système d'actifs numériques pair-à-pair qui permet l'échange direct de valeur sans passer par une partie centrale. Le protocole Bitcoin original [1] fournit ce qui est nécessaire pour créer un système de trésorerie électronique pair-à-pair, mais il ne possède pas la capacité de vérifier les historiques de transactions et, par conséquent, ne peut pas être utilisé pour valider des actifs numériques. Les signatures numériques et les contraintes de sortie fournissent une partie de la solution, mais les principaux avantages sont perdus si une tierce partie de confiance est encore nécessaire pour valider les actifs numériques. De manière similaire à Bitcoin, le réseau Radiant ne nécessite qu'une structure minimale et horodate les transactions dans une chaîne de preuve de travail basée sur le hachage. Nous introduisons deux techniques pour valider les actifs numériques : des références uniques et un système de preuve d'induction général à la fois opérant dans un temps et un espace constants O(1). Il est possible de composer les sorties de n'importe quelle manière, sans compromettre les caractéristiques intrinsèques de parallélisme et de performance d'une architecture basée sur les sorties de transaction non dépensées (UTXO). Par conséquent, les utilisateurs peuvent quitter et rejoindre le réseau Radiant à leur guise et être assurés de l'intégrité et de l'authenticité de leurs actifs numériques.

# 1. Introduction

Le commerce avec les blockchains, ou la technologie de registre numérique (DLT), repose souvent sur des émetteurs et des gardiens agissants en tant que parties de confiance pour authentifier les actifs numériques. Bien que ces systèmes fonctionnent assez bien pour les transactions de type paiement électronique, ils souffrent toujours des faiblesses inhérentes du modèle de confiance pour les utilisations avancées. Les blockchains basées sur la machine virtuelle Ethereum (EVM) [2] sont très flexibles pour tous les types de programmes, mais les frais élevés les rendent impraticables pour les applications de micropaiement.

Ce dont nous avons besoin, c'est un système de paiement électronique pouvant être utilisé pour un système de gestion d'actifs numériques avec des frais réduits, une haute performance et des capacités de programmation avancées. Dans cet article, nous proposons une solution au problème de l'expansion et de la contraction des blockchains en utilisant deux techniques novatrices qui fournissent des références uniques et un système général de preuve par induction, ce qui permet des programmes de Turing complet [3] à travers les frontières de transaction. Le système proposé est décentralisé, utilise un mécanisme de consensus de preuve de travail comme Bitcoin, mais avec des niveaux de débit considérablement plus élevés, tout en offrant la même flexibilité que les blockchains basées sur EVM, avec des frais très bas.

# 2. Transactions

Similaire à Bitcoin, nous définissons une pièce électronique comme une chaîne de signatures numériques. Ce qui distingue les transactions dans Radiant, c'est que chaque propriétaire transfère la pièce au suivant en signant numériquement un hachage de la transaction précédente, en plus des paramètres d'entrée requis pour déverrouiller la pièce. Une transaction crée également de nouvelles contraintes de verrouillage de sortie, qui peuvent inclure la clé publique du prochain propriétaire, parmi toutes les autres règles définies par l'utilisateur.

 ![Diagram 1. Radiant Transactions.](images/w2.jpeg)
>**Diagram 1.** Transactions Radiant.

Pour vérifier qu'une double dépense ne s'est pas produite, nous utilisons un serveur d'horodatage distribué, en utilisant un système de preuve de travail basé sur le hachage pour organiser l'historique canonique afin de déterminer quelle transaction est arrivée en premier. Les transactions sont organisées en blocs. Par convention, la première transaction, appelée "transaction de coinbase", dans un bloc est une transaction spéciale qui démarre une nouvelle pièce appartenant au créateur du bloc. Les blocs sont chaînés ensemble et organisent les transactions en un arbre de Merkle [4]. Toutes les transactions, à l'exception de la première, doivent référencer une transaction précédente formant un graphe acyclique dirigé (DAG) où toutes les pièces se connectent finalement à au moins l'une des transactions spéciales au début d'un bloc.

![Diagram 2. Block Structure; transactions are organized into a Merkle Tree.](images/w3.jpeg)
>**Diagram 2.** Structure des blocs ; les transactions sont organisées dans un arbre de Merkle.

Le problème avec cette conception, dans le contexte des actifs numériques, est qu'il n'y a qu'un seul type de pièce ou d'actif numérique, et pas de concept de pièces définies par l'utilisateur (ou de types d'actifs numériques). La conception fonctionne assez bien pour les transactions électroniques de type paiement dans l'unité de compte native, mais elle ne se prête pas immédiatement à être utilisée pour d'autres types de pièces ou d'actifs numériques. Une solution courante consiste à introduire un service tel qu'un indexeur de transaction qui surveille les transactions pour détecter des séquences de données spéciales signalant la création d'un actif numérique. Le problème avec cette solution est qu'elle dépend de l'entreprise qui exécute le service, l'authenticité de l'actif numérique devant être de confiance, tout comme tout autre service sur le Web.

Nous avons besoin d'un moyen pour que les utilisateurs puissent indiquer la création de types de pièces personnalisées, mais sans dépendre d'un service de confiance pour être utilisé pour la présentation de données.

# 3. Actifs Numériques

Nous définissons une pièce électronique personnalisée, ou un actif numérique, comme une chaîne de signatures électroniques. Un actif numérique est un type de pièce défini par l'utilisateur à l'aide d'un marqueur de transaction spécial, appelé une "transaction d'actif de base", pour créer ou émettre un actif numérique. De manière similaire aux transactions Coinbase, qui injectent de nouvelles pièces dans le système, la transaction d'actif de base colore ou étiquette la pièce électronique avec un identifiant unique de 36 octets pour sa durée de vie. La pièce électronique personnalisée est superposée au type de pièce de base et fonctionne de manière similaire. La transaction d'actif de base peut apparaître n'importe où dans le bloc et peut imposer toutes les règles et contraintes personnalisées décidées à l'avance.

![Diagram 3. Transactions representing user-defined
coin types &mdash; or digital assets.](images/w4.jpeg)
>**Diagram 3.** Transactions représentant des types de pièces définis par l'utilisateur &mdash; ou des actifs numériques.

Pour réaliser cela, nous devons créer un identifiant unique stable et un mécanisme de transaction pour suivre l'authenticité du type de pièce (actif numérique). Les utilisateurs du système doivent avoir la preuve que les types de pièces personnalisées ne sont pas des contrefaçons et représentent avec précision les actifs numériques.

![Diagram 4. Custom user-defined coin types are defined from a special mint transaction. A unique identifier is used to classify the coin type.](images/w5.jpeg)
>**Diagram 4.** qui montre que les types de pièces personnalisées définies par l'utilisateur sont créés à partir d'une transaction spéciale de "mint". Un identifiant unique est utilisé pour classer le type de pièce.

# 4. Identifiants Uniques

Pour mettre en place un identificateur unique pour un type de coin, nous utilisons une transaction marqueur spéciale, appelée "transaction d'actif de base", qui agit comme le début (la création) de la chaîne de signatures numériques. Au lieu de nécessiter une nouvelle structure de données pour l'identificateur unique, nous réutilisons l'identifiant de transaction et l'index de sortie, appelé un "outpoint", comme identificateur unique pour le type de coin. Il est assuré que les outpoints (36 octets) sont aléatoires et globalement uniques.

Une instruction de programmation, appelée OP\_PUSHINPUTREF, est utilisée pour attacher une référence à une sortie. L'instruction accepte exactement un paramètre de 36 octets qui doit correspondre soit 1) au point de sortie de l'une des sorties dépensées, ou 2) à la même valeur de 36 octets qui apparaît déjà dans un OP\_PUSHINPUTREF spécifié précédemment dans l'une des sorties dépensées.
La seule façon pour une valeur donnée d'apparaître dans une sortie de transaction est que, grâce à une transaction ancêtre, elle correspondait au point de sortie de la transaction initiale de création d'actifs. Les transactions qui spécifient une valeur qui ne répond pas à l'une ou l'autre de ces conditions sont invalides.

![Diagram 5. Unique identifiers are initialized by matching an outpoint of one of the outputs being spent, and then maintained as long as at least one of the outputs being spent contains the same unique identifier in the script body.](images/w6.jpeg)
>**Diagram 5.** Les identifiants uniques sont initialisés en associant un outpoint de l'une des sorties dépensées, puis sont maintenus aussi longtemps qu'au moins l'une des sorties dépensées contient le même identifiant unique dans le corps du script.

Cette simple instruction de programmation fournit un identifiant unique qui peut être utilisé comme une référence stable pour créer des règles avancées. Par exemple, différents types de pièces, des actifs numériques, peuvent maintenant dépendre d'autres types de pièces. Comme toutes les données sont locales à la transaction, via ses transactions d'entrée parent immédiates, il est facile pour les clients et les services de valider l'authenticité d'un actif numérique en temps constant O(1) et d'espace, évitant ainsi le besoin d'un service de confiance.

# 5. Preuves par induction

Il est possible de créer des identifiants uniques d'une manière alternative et de fournir également un mécanisme de preuves par induction mathématique en utilisant un algorithme de hachage de transaction modifié. En permettant aux scripts d'entrée d'accepter la transaction parent dépensée, les règles peuvent vérifier que le parent, ainsi que son grand-parent, respectent les règles requises. Le problème évident est que chaque copie complète des transactions parent est intégrée, une explosion de taille exponentielle se produit et empêche l'utilisation pratique de la technique. Ce qui est nécessaire, c'est une façon de compresser la transaction, de sorte qu'une structure de données de taille fixe puisse être utilisée à la place pour dériver le hachage de la transaction, au lieu de nécessiter le contenu complet de la transaction.

![Diagram 6. Full parent transaction validation, mathematical induction proof by embedding the full parent transactions into the inputs resulting in exponential transaction size increase.](images/w7.jpeg)
>**Diagram 6.** Validation complète de la transaction parent, preuve par induction mathématique en intégrant les transactions parent complètes dans les entrées, entraînant une augmentation exponentielle de la taille de la transaction.

Nous pouvons y parvenir en modifiant l'algorithme de hachage de transaction utilisé dans Bitcoin, où une somme de contrôle double sha-256 est calculée à partir de la transaction sérialisée, en une nouvelle version qui résume d'abord le contenu de la transaction pour dériver le hachage. Nous introduisons l'algorithme de hachage de transaction version 3, pour le distinguer de l'utilisation des versions 1 et 2 dans Bitcoin. Le processus consiste à hacher chaque champ ou composant d'une transaction, pour obtenir un hachage intermédiaire, qui peut être utilisé comme entrée de taille fixe et ainsi éviter la croissance exponentielle de la taille de la transaction.

Nous utilisons la structure de données suivante de 112 octets, au lieu des octets de transaction sérialisés complets, qui sont à leur tour hachés en double sha-256 pour obtenir le hachage de transaction final.

Voici la structure de données de 112 octets pour l'antécédent de hachage de transaction de version 3, qui est utilisée à la place des octets de transaction sérialisés complets :

Champs d'antécédents de hachage de transaction de version 3 :
> 1. nVersion (= 3) de la transaction (4 octets en petit endian)
> 2. nTotalInputs (4 octets en petit endian)
> 3. hashPrevoutInputs (32 octets de hachage)
> 4. hashSequence (32 octets de hachage)
> 5. nTotalOutputs (4 octets en petit endian)
> 6. hashOutputHashes (32 octets de hachage)
> 7. nLocktime de la transaction (4 octets en petit endian)

En utilisant l'algorithme de hachage de transaction pour les transactions de version 3, nous sommes en mesure d'intégrer la transaction parent et la transaction grand-parent à chaque étape d'une preuve d'induction mathématique pour éviter une augmentation de la taille de la transaction, et pouvons appliquer toutes les règles nécessaires.

![Diagram 7. Compressed parent transaction validation, mathematical induction proof by embedding the transaction hash version 3 preimage data-structure of the parent and grand-parent to enforce arbitrary rules and constraints.](images/w8.jpeg)

>**Diagram 7.** Validation compressée de la transaction parent, preuve par induction mathématique en intégrant la structure de données d'antécédent de hachage de transaction de version 3 de la transaction parent et de la transaction grand-parent pour appliquer des règles et des contraintes arbitraires.

# 6. Réseau

La topologie du réseau est un graphe presque complet, dans lequel chaque nœud de minage est connecté à tous les autres nœuds de minage. Les étapes pour exécuter le réseau sont les mêmes que pour Bitcoin, avec quelques distinctions pour les différents types de nœuds : les nœuds de minage, les nœuds agents et les nœuds d'archives. Les nœuds de minage sont les éditeurs actifs de blocs et maintiennent le consensus avec tous les autres nœuds, les nœuds d'archives fournissent des données de bloc historiques, et les nœuds agents sont conçus pour filtrer les blocs et suivre les transactions d'intérêt pour les applications qu'ils servent. Les nœuds d'archives et les nœuds agents peuvent opérer sur le même réseau pair-à-pair mais ne produisent pas de blocs. Les nœuds non-miniers tels que les nœuds d'archives et les nœuds agents sont parfois appelés "nœuds d'écoute" pour distinguer leur rôle dans le réseau.

![Diagram 8. Mining Nodes are well-connected and build on top of each other's blocks. Archive nodes store complete blocks for historical analysis and bootstrapping purposes. Agent nodes are listener nodes which filter and store transactions to serve clients.](images/w9.jpeg)

>**Diagram 8.** Les nœuds de minage sont bien connectés et construisent sur les blocs les uns des autres. Les nœuds d'archive stockent des blocs complets pour l'analyse historique et le démarrage. Les nœuds agents sont des nœuds d'écoute qui filtrent et stockent les transactions pour servir les clients.

Les nœuds de minage sont bien connectés dans un graphe presque complet avec d'autres nœuds de minage. Leur travail consiste à construire sur les blocs les uns des autres et à maintenir un consensus pour les quelques centaines de blocs les plus récents, ainsi qu'à maintenir l'ensemble des UTXO pour prévenir les doubles dépenses.

Les nœuds agents n'ont besoin de stocker qu'un sous-ensemble de transactions, par exemple, un type de pièce ou un actif numérique spécifique. Même avec de grands blocs, un nœud agent peut rapidement filtrer les transactions par références ou séquences de bytes spécifiques, puis stocker ces transactions pour les servir via une interface de programmation d'application. Entre agents coopérants, une racine d'arbre de Merkle peut être annoncée publiquement pour les transactions dans chaque bloc qui correspondent à un modèle prédéterminé, afin de signaler aux autres agents et aux consommateurs quelles transactions cet agent a traitées.

Les nœuds d'archive sont utilisés pour créer des copies de sauvegarde de blocs entiers pour diverses applications, notamment la conservation de données, l'analyse et l'apprentissage automatique. Étant donné que les nœuds d'archive ne sont pas directement impliqués dans le minage, ils n'ont pas les mêmes exigences de performances en temps réel et de bande passante que les nœuds de minage ou d'agents.

# 7. Calculs

Nous examinons le scénario dans lequel le réseau Radiant continue de croître et ce que cela implique pour les exigences de traitement des nœuds miniers, d'archivage et d'agent. Pour comparaison, au moment de la rédaction, il y a environ 83 millions de sorties de transaction non dépensées pour la blockchain Bitcoin, pour un total d'environ 6 Go de données nécessaires pour éviter les doubles dépenses. Il est seulement nécessaire que les quelques centaines de blocs les plus récents soient conservés par les nœuds miniers, les blocs plus anciens étant disponibles auprès des nœuds d'archivage. Pour les nœuds d'agent, il est nécessaire de conserver la partition pertinente de sorties de transaction non dépensées qui concernent les applications qu'ils desservent, la mise à l'échelle étant une fonction de la bande passante et non des exigences de stockage.

Pour nos besoins, nous supposons qu'il y aura des blocs de 3 Go toutes les 5 minutes à horodater et à distribuer sur le réseau, soit environ 20 000 transactions par seconde avec une taille de transaction moyenne de 500 octets, soit environ 6 000 000 transactions par bloc. Nous montrons que pour chaque type de noeud, le réseau est capable de se développer suffisamment pour répondre à la demande mondiale. Cela équivaut à environ 1 transaction tous les 5 jours pour chacun des 8 milliards de personnes sur la planète.

### Noeuds de minage

Les noeuds miniers (Mining Nodes) sont les seuls types de nœuds à construire sur les blocs des autres. Pour maintenir un consensus, il suffit de synchroniser l'ensemble des sorties de transactions non dépensées (UTXO) et de ne conserver que les cent derniers blocs. Au moment de la rédaction, les disques durs bon marché à haute performance sont capables de réaliser plus de 120 000 E/S par seconde, coûtant environ 500 USD pour 280 Go, et peuvent donc gérer environ 20 000 transactions par seconde (en supposant que chaque transaction a 2 entrées et 2 sorties). Il y a 2 lectures pour les entrées, 2 mises à jour des entrées et 2 écritures pour la nouvelle sortie: 120 000 / 6 = 20 000 transactions/seconde.

### Les nœuds d'archivage

Les Archive Nodes fournissent des données de blocs historiques et conviennent aux applications de machine learning, d'analyse et d'entreposage de données. Les Archive Nodes peuvent compléter le démarrage des Mining Nodes et exécuter des vérifications de cohérence périodiques sur l'ensemble des transactions non dépensées (UTXO). Au moment de la rédaction, des disques durs de 18 To sont disponibles pour environ 350 USD. En supposant 3 Go de données toutes les 5 minutes, cela équivaut à une exigence de stockage de 732 Go par jour, soit environ 22 To par mois. Le coût matériel pour une année est de 15 disques durs, avec une capacité de 18 To, pour un coût incrémentiel annuel de 5 000 USD.

### Les nœuds d'Agent

Les nœuds d'agent se mettent à l'échelle de la manière la plus facile parmi les types de nœuds car ils ne traitent que les types de transactions pertinents pour les applications qu'ils servent. Par conséquent, les nœuds d'agent peuvent aller d'un serveur web à un appareil IoT léger avec des capacités de traitement et de stockage limitées, et pourtant suivre le rythme des blocs de 3 Go, ou 20 000 transactions par seconde. Par exemple, une entreprise peut souhaiter suivre l'utilisation de ses points de fidélité, créés en tant qu'actif numérique, et n'a donc besoin de sélectionner qu'un petit sous-ensemble de mises à jour de transactions de chaque bloc qui correspondent à l'identifiant unique de ce type de pièce.

Au moment de la rédaction, un dispositif informatique commercial, le Raspberry Pi 4, est vendu environ 275 USD, il dispose d'un processeur quadcore de 1,5 GHz et de 4 Go de RAM, qui peut être utilisé pour filtrer rapidement et éliminer les transactions non pertinentes à une vitesse de 5 000 transactions par cœur. Bien sûr, il ne s'agit là que d'un exemple de la facilité à traiter de grands blocs, dans une application Web typique, il peut y avoir beaucoup plus de cœurs disponibles.

La vitesse de bande passante médiane des 25 premiers pays dépasse 100 Mb/s, soit environ 10 Mo/seconde de téléchargement, de nombreux fournisseurs de services Internet offrant des téléchargements illimités. Les exigences en bande passante pour des blocs de 3 Go toutes les 5 minutes sont d'environ 10 Mo/seconde pour un total de 22 To par mois. Des hiérarchies de nœuds Agent peuvent également être créées pour réduire les exigences totales de bande passante pour les nœuds Agent ayant une capacité de bande passante plus faible.

# 8. Conclusion

Nous avons proposé un système de gestion d'actifs numériques sans avoir besoin de faire confiance à une tierce partie. Nous avons commencé par les blocs de base de pièces faites à partir de signatures numériques, qui fournissent un contrôle fort de la propriété. À partir des règles et des incitations nécessaires, nous avons introduit deux méthodes novatrices pour authentifier et suivre les actifs numériques en temps et en espace constants O(1). Les deux méthodes fournissent indépendamment un système de preuve par induction mathématique général qui peut encoder n'importe quelle configuration d'actifs numériques possible. Le système Turing est complet dans et entre les limites de transaction, sans avoir besoin de couches secondaires. Radiant est une conception révolutionnaire qui offre les avantages de performance et de parallélisme d'une blockchain de sortie de transaction non dépensée (UTXO), mais avec la capacité de contracter des blockchains basées sur des comptes en utilisant la machine virtuelle Ethereum (EVM).

# References

[1] Satoshi Nakamoto, "Bitcoin: A Peer-to-Peer Electronic Cash System" URL

https://bitcoin.org/bitcoin.pdf, 2009.

[2] Vitalik Buterin, "Ethereum: A Next-Generation Smart Contract and Decentralized

Application Platform." URL https://ethereum.org/en/whitepaper/, 2014.

[3] Wikipedia contributors. "Turing completeness." Wikipedia, The Free Encyclopedia.

Wikipedia, The Free Encyclopedia, URL https://en.wikipedia.org/wiki/Turing\_completeness,

21 Jul. 2022

[4] R.C. Merkle, "Protocols for public key cryptosystems," In Proc. 1980 Symposium on Security and Privacy, IEEE Computer Society, pages 122-133, April 1980.

[5] Britannica, T. Editors of Encyclopaedia. "mathematical induction." Encyclopedia Britannica, URL https://www.britannica.com/science/mathematical-induction, 2018
