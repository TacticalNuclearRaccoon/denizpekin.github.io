---
date: 2026-08-25 10:52:42
layout: post
title: "Est-ce que l'IA pourrait vaincre l'entièreté de l'humanité?"
subtitle: "La préface d'une série sur la philosophie de l'IA"
description: Ceci est le premier chapitre d'une série de réflections philosophiques autour de l'IA
image: https://res.cloudinary.com/dkdnwcvui/image/upload/v1787849680/Screenshot_2026-08-27_at_18.54.34_qfy5mn.png
optimized_image: https://res.cloudinary.com/dkdnwcvui/image/upload/v1787849680/Screenshot_2026-08-27_at_18.54.34_qfy5mn.png
category: blog
tags: 
    - IA
    - sécurité
    - philosophie
    - science-fiction
author: deniz
paginate: false
---

J'ai récemment joint un club de lecture international. Toutes les semaines on se réunit par petits comités et on discute autour des questions concernant la sécurité de l'Intelligence Artificielle (IA). Mais le sujet n'est pas la cybersécurité ou MLSecOPS (Machine Learning Security Operations). Non, on parle carrément des scénarios à la <a href="https://en.wikipedia.org/wiki/Skynet_(Terminator)"> *Skynet* </a> (Terminator), ou <a href="https://en.wikipedia.org/wiki/HAL_9000"> *HAL* </a> (2001 Space Odyssey). On discute de l'éventualité que l'IA; intelligente, consciente, dotée du pouvoir d'action, devienne un risque existentielle pour l'humanité. Et ce qu'on pourrait faire avant, pendant et après cette éventualité... 

La raison pour laquelle j'ai rejoint ce club de discussion c'est surtout parce que moi, personnellement je ne crois pas du tout au **Skynet**. Un modèle statistique capable de produire le bout de mot le plus probable dans une conversation (je parle plus précisement des LLMs) ne va pas détruire l'humanité (à mon humble opinion). Par contre le capitalisme, les inégalités, les projets dystopiques, la désinformation et la manipulation des opinions rendus possible par cette technologie, oui, tout ça va sûrement détruire l'humanité mais cette destruction pourrait parfaitement se réaliser avec une technologie autre que l'IA. 

En plus de cela, je pense sincérement qu'il y a un alarmisme fabriquée autour de l'IA par les grandes entreprises qui commercialisent des LLMs (comme Anthropic ou OpenAI) pour pousser les gouvernements à réguler les modèles Open Source par pure intêrets commerciaux.

![placeholder](https://res.cloudinary.com/dkdnwcvui/image/upload/v1787849311/Screenshot_2026-08-27_at_18.48.23_vgj0p6.png)
*La réponse de Yann LeCun à l'alarmisme autour du risque existentiel lié à l'IA*

J'étais curieuse de voir ce que ces personnes de très différents horizons (ingénieurs, journalistes, philosophes, médecins, etc.) toutes beaucoup plus intelligentes que moi, pensaient sur le sujet, et surtout pourquoi elles étaient inquiètes, et pas moi.  

Mais je voudrais d'abord partager comment moi je perçois l'IA. 

Je tiens aussi à préciser un dernier point : de nos jours quand on parle de l'IA, on fait souvent l'amalgame avec les Grand modèle de langage (Large Language Models : LLMs) ou l'IA générative au sens large. Même si ce n'est qu'un sous ensemble des modèles d'IA qui existent, personne n'est en train de s'inquièter que <a href="https://fr.wikipedia.org/wiki/AlexNet"> AlexNet </a> va conquérir le monde. Donc pour le reste de cet article je me permets de continuer cet amalgame. 

## Au delà du Test de Turing

<a href="https://en.wikipedia.org/wiki/Turing_test"> Le Test de Turing </a> n'est pas suffisamment robuste en tant que benchmark pour l'antropomorphicité d'un système non-humain (c'est mon humble opinion bien sûr). Les LLMs le passent haut la main, tandis que certains de mes amis l'échouent lamentablement des fois. 

Dans les années 1960, Joseph Weizenbaum écrivit un programme informatique appelé <a href="https://dl.acm.org/doi/10.1145/365153.365168"> Eliza </a>. Il s'agissait d'un chatbot "thérapeutique" assez simple. L'utilisateur écrivait quelque chose dont il voulait se plaindre, par exemple "mon repas de midi n'était pas bon," auquel Eliza repondrait "Dis-moi comment c'était pas bon". Le but de Weizenbaum était de faire une petite expérimentation drôle juqau'à ce qu'il décide d'éteindre la machine qui hébergait Eliza et il se rendit compte que sa secrétaire ne pouvait pas se passer de parler à Eliza tous les jours. Non seulement elle, il y avait plusieurs autres personnes qui parlaient à Eliza pendant des heures. Cette anecdote pourrait à elle seule constituer le "business pitch" de character.io et inutile de dire que Eliza (qui n'est pas une IA par aucune définition du terme) passe le test de turing.

La capacité à pouvoir passer le test de Turing ne suffit pas à expliquer l'illusion d'intelligence fournie par les LLMs. Mais il existe des analogies qui permettent de nous éclairer un peu sur cette illusion. Mes deux favoris sont *la pièce chinoise* et *le pieuvre commère*. 

Dans le premier, on doit imaginer une pièce un peu comme celles qu'on a dans les batiments administratives, avec une personne assise à l'intérieur. Il y a une séparation en verre depuis laquelle vous pouvez passer des feuilles de papiers, des enveloppes, etc. Ici on s'échange des cartes. Sur chaque carte il y a un mot écrit en Chinois. La personne à l'intérieur de la pièce reçoit la carte, lit ce qui est marqué dessus et donne une carte depuis sa pile de cartes sur laquelle est marquée une réponse. L'astuce c'est que la personne ne parle pas un mot de Chinois. Chaque carte est marquée par un code couleur. Il reçoit une carte rouge (imaginons que sur cette carte il est écrit "Bonjour, comment tu vas?") et en échange il donne une carte rouge (sur laquelle sera écrit la réponse correspondante "Je vais bien, merci"). 
Quelqu'un qui ne connait pas ce fonctionnement pourrait parfaitement croire que la personne parle Chinois, au moins qu'il comprennne suffisamment pour produire la bonne carte. Mais ce n'est clairement pas le cas. Même les personnes qui ont construit ce système n'ont pas besoin de parler Chinois si on imagine qu'ils ont construit leur code couleur en étudiant des centaines de milliers de conversations sur des forums internet en Chinois.

L'analogie de la pieuvre est similaire mais un peu plus Lovecraftien. Deux personnes sont échouées sur deux îles désertes à plusieurs kilométres l'une de l'autre. On va les appeler Alice et Bob (pour choisir des noms <a href="https://en.wikipedia.org/wiki/Alice_and_Bob"> complétement par hasard </a>). Assez rapidement, Alice découvre que son île avait été habitée par une civilisation et les précedents habitants ont laissé un télegraphe. Avec un système assez sophistiqué de câbles  qui passent sous l'océan, le telegraph permet d'envoyer des messages vers l'île où se trouve Bob et recevoir des réponses. Bob ne tarde pas à faire la même découverte et ils commencent à discuter tous les jours. Pendant ce temps Mallory, qui est une pieuvre des grands fonds dotée d'une intelligence exceptionnelle et d'une curiosité hors paire découvre un moyen de se connecter aux câbles sous-marins et d'écouter les conversations de Alice et Bob. 
Alice et Bob discutent en Anglais. Non seulement Mallory ne parle pas Anglais, mais il n'a jamais vu d'humains et n'a aucun moyen de se rendre aux îles pour les observer eux ou leurs environnements respectives. Mais il est extremment doué à décerner des tendances statistiques. Au fil du temps Mallory remarque également que certains mots ont tendance à apparaître dans des contextes similaires, et apprend peut-être à généraliser ces schémas lexicaux en émettant l'hypothèse qu'ils peuvent être utilisés de manière quelque peu interchangeable. Néanmoins, Mallory n'a jamais observé ces objets. Quand Alice et Bob parlent d'un noix de coco, Mallory peut correctement mettre cet objet dans sa place au sein du contexte de la conversation, mais si jamais il pouvait se rendre sur une des îles, il ne pourrait pas pointer la vrai noix de coco d'une de ses tentacules.  
Un jour, juste pour s'amuser, Mallory coupe le cablage et s'immisce dans la conversation, en se faisant passer pour Bob. Est-ce que Alice pourrait se rendre compte qu'il y a quelque chose qui ne va pas? 
S'il s'agissait d'un Test de Turing, Alice est à son désavantage car elle n'a aucune raison de supçonner qu'elle parle à quelqu'un d'autre que Bob. Ils sont sur deux îles désertes après tout. Tant que la conversation reste dans les plaisanteries de tous les jours, Mallory peut exercer sa fonction de chatbot et continuer à duper Alice tout comme Eliza l'aurait fait.  
Maintenant imaginons qu'il se passe quelque chose qui sort de l'ordinaire. Alice a une nouvelle invention : une catapulte à noix de coco. Pour Mallory une catapulte est un nouveau concept. Il ne dispose d'aucune référence pour imaginer ce qui se passe en réalité. Mais il a étudié un tas de conversations ou Alice parlait de ses inventions qui étaient similairement construites avec des ficelles et des planches de bois. Mallory décide de répondre : "Très originale comme idée ça. Bravo!"
Si Alice trouve cette réponse naturelle et acceptable (ce qui est toute à fait possible), c'est elle qui se charge entièrement d'attribuer un sens à la réponse de Mallory. Ce n'est pas Mallory qui a compris le sens de ses mots ni même le message initiale de Alice.     

![placeholder](https://res.cloudinary.com/dkdnwcvui/image/upload/v1787755653/octopus2_k2ucg7.png)

J'ecris tout ça pour dire que la capacité d'une entité (d'un sytème ou d'un programme) de se faire passer pour "intelligente" dépend aussi en partie du jugement des personnes qui sont en communication avec cette entité. Ces personnes ont un rôle actif et sont sujettes à des biais comme <a href="https://fr.wikipedia.org/wiki/Anthropomorphisme"> l'antropomorphisme </a> ou <a href="https://fr.wikipedia.org/wiki/Par%C3%A9idolie"> paridéolie </a> devant ces entités. 

Pour toute personne septique à l'émergence d'une intelligence artificielle consciente et dotée de sa volonté propre, la conversation s'arrête là. Il n'y a pas de raison de s'inquiéter d'une éventuelle extinction de l'humanité menée par l'IA (le capitalisme dans sa phase avancée pourrait très bien s'occuper de cela, même avec n'importe quelle autre technologie). Le battage médiatique exagère la nature et les capacités des LLMs qui ne sont pas très différents de l'homme dans la pièce chinoise ou Mallory.  

Pourtant je n'arrête pas la conversation là et j'ai même écrit une série d'articles de plusieurs chapitres sur le sujet. Parce que même si il est naturel (pour moi) de dire que les LLMs, surtout dans leur état actuel, ne sont pas vraiment intelligents et ne peuvent pas devenir conscients, je ne pourrais pas donner une vraie définition de l'intelligence, ni de la conscience, ni articuler (avec suffisamment de confiance) ce qui nous rend intelligents ou conscients. 

Pour moi cela rend le sujet digne de réflexion. Après tout, une poignée de personnes très capables, dirigeant les entreprises les plus puissants dans le monde se sont donnés explicitement la priorité de créér une <a href="https://www.theverge.com/2024/1/18/24042354/mark-zuckerberg-meta-agi-reorg-interview"> intelligence artificielle générale (AGI) </a>. Si on ne réfléchit pas sur le sujet maintenant, on risque de somnambuler vers un futur où c'est déjà là. 

## L'Intelligence Artificielle en tant que risque existentielle 

Pensons un instant que, malgré notre septicisme sur le sujet, un scénario à la Skynet se produit. C'est à dire qu'un modèle LLM acquiert une volonté qui lui est propre, devient conscient et décide de détruire l'humanité. Ça serait un risque existentiel et en ce qui concerne les risques en général, une **risque extistentiel** est plutôt sérieux. 

Pour n'importe quelle menace existentielle, le vrai risque c'est notre capacité à le prédire. Par exemple si X est une catastrophe dont la probabilité de survenue P(X) est extrêmement faible et si A est la méthode scientifique qui permet de mesurer cette probabilité, l'essentiel du risque réside dans la probabilité qu'A présente une faille cruciale pour mesurer P(X). Si P(X) est extrêmement faible, la probabilité que X se produise étant donné que A est imparfaite sera élevée.

Dans notre cas, même si on pense que la probabilité que l'humanité soit exterminée par une IA est extrèmement faible, on n'a pas vraiment de méthodes fiables pour assurer que cette probabilité est effectivement faible. C'est pour ça que je pense qu'il est intéressant de réflechir à ce qu'on pourrait faire si jamais cela arrivait et surtout, comment on saurait si c'était en train d'arriver.

Une IA qui déciderait d'exterminer l'humanité serait une IA mal-alignée. Je voudrais parler de la question de **l'alignement** plus en détail dans le chapitre suivant (**Chapitre 1**). Ici je vais me contenter de préciser qu'il s'agit de l'alignement de valeurs, un semblable des <a href="https://fr.wikipedia.org/wiki/Trois_lois_de_la_robotique"> 3 lois de la robotique </a> dans les romans de Asimov, qui dictent à l'IA d'agir de la façon qui est considérée comme "bonne". 

Dans son article <a href="https://www.cold-takes.com/ai-could-defeat-all-of-us-combined/"> "Could AI defeat all of us combined?" </a> Holden Karnofsky argumente qu'une IA mal-alignée aurait plusieurs capacités à sa disposition qu'elle pourrait utiliser contre nous : 

1. Mener ses propres recherches sur la manière de construire un meilleur système d’IA, ce qui aboutit à la création d’une entité dotée d’autres capacités inattendues.
2. Pirater des logiciels ou <a href="https://openai.com/index/hugging-face-incident-and-the-road-ahead/"> des infrastuctures </a> créés par les humains partout dans le monde. 
3. Manipuler la <a href="https://en.wikipedia.org/wiki/AI-induced_psychosis"> psychologie humaine </a>.
4. Rapidement <a href="https://www.lesswrong.com/posts/XRADGH4BpRKaoyqcs/the-first-confirmed-instance-of-an-llm-going-rogue-for"> générer d’immenses richesses </a> sous son propre contrôle ou celui de ses alliés humains.
5. Élaborer des plans plus efficaces que tout ce que les humains pourraient imaginer, tout en veillant à ne pas tenter de prise de pouvoir trop flagrante que les humains seraient susceptibles de détecter et d’empêcher.
6. Développer des armes sophistiquées pouvant être fabriquées rapidement et à moindre coût, mais suffisamment puissantes pour dominer les armées humaines.

J'ai souligné des évènements plus ou moins récents où des LLMs actuels ont montré certains de des capacités dans cette liste. 

En général, il n'y a pas beaucoup de choses qui pourraient mettre fin à la civilisation humaine si elles *s'y mettaient*. Si nous en créons une (exprès ou pas), je pense que nous devrions nous demander non pas "Pourquoi cela serait-il dangereux?", mais plutôt "Pourquoi ne le serait-ce pas?"  

