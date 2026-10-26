# Cassos Deck

Application Windows de collection et de combat de cartes personnalisées. Les cartes,
photos, catégories et identifiant joueur sont enregistrés localement dans `%APPDATA%\CassosDeck`.

## Créer l'exécutable Windows

Installe Python 3.10 ou plus récent, puis lance `build.ps1` depuis PowerShell.
Le fichier final sera créé dans `dist\Cassos Deck P2P v9.exe`.

## Utilisation

- Crée une carte, ajoute une photo, donne-lui un nom et choisis une seule catégorie.
- Chaque catégorie fournit ses attaques fixes, qui ne peuvent être ni ajoutées, ni supprimées,
  ni modifiées. Toutes les cartes ont exactement 100 PV.
- Les anciennes cartes sont converties automatiquement : leur première catégorie dans l'ordre
  de la liste devient leur catégorie unique, leurs PV passent à 100 et leurs attaques sont
  remplacées par celles de cette catégorie.
- Le détourage est effectué localement et fonctionne mieux sur un fond uni ou contrasté.
- Recherche et filtre ta collection. Une nouvelle carte est ajoutée en tête et apparaît
  immédiatement, quels que soient la recherche ou le filtre actif.
- Exporte les cartes actuellement visibles (toute la collection sans filtre, ou le résultat
  de la recherche et de la catégorie sélectionnées) dans un fichier `.csd`. Importe un `.csd`
  pour ajouter ses cartes à ta collection sans écraser les cartes existantes ; les doublons
  restent séparés et reçoivent un nouvel identifiant local.
- Un fichier `.csd` est une archive ZIP renommée contenant les noms, catégories, 100 PV,
  attaques fixes et photos des cartes. L’import vérifie le format et conserve toutes les
  données en local.
- Chaque installation reçoit un identifiant joueur créé hors ligne et conservé localement.

## Attaques prédéfinies

- **HRP** : Force le destin de manière irréaliste — 70 PV ; immobilise la carte attaquante
  pendant un tour.
- **HRP Pur** : Spawn Kill — 45 PV à la dernière carte posée par l'adversaire.
- **Raider** : Braquage Express — 40 PV et défausse d'une carte adverse choisie dans sa main ;
  Quitte-retour — restaure tous les PV de la carte qui l'utilise ; Bullseye — 40 PV à toutes
  les cartes.
- **Cheater** : Speedhack — deux frappes de 25 PV, chacune avec sa cible ; Aimbot Perfect —
  80 PV à toute carte adverse disponible ; Godmode — invulnérabilité pendant le tour adverse
  qui suit.
- **Car killer** : Ram-Vehicle — 65 PV et paralysie pendant un tour ; Piège à Hérissons —
  20 PV à chaque carte ennemie et dégâts réduits de moitié au prochain tour ; Car-Bombing —
  50 PV à la cible, puis 25 PV à chaque carte de l'équipe adverse.
- **Gâchette facile** : Tir de Suppression (M4A1) — 25 PV à toutes les cartes sur le terrain
  adverse ; Double Tap (Shotgun) — 75 PV à la carte en face ; Rafale d'Avertissement —
  quatre frappes de 15 PV, réparties librement ou concentrées sur la même carte.
- **Revenger** : Œil pour Œil — renvoie 50 % des dégâts subis au tour précédent à leurs
  attaquants ; Dernier Souffle — 90 PV si la carte qui attaque a moins de 30 % de ses PV ;
  Vendetta Familiale — 60 PV à une cible adverse si un allié a été éliminé au tour précédent.

## Parties entre joueurs

Sans serveur central : l'hôte écoute sur le port TCP 45123 et partage son adresse `IP:port`
et le code de partie aléatoire de 32 caractères hexadécimaux. Sur le même Wi-Fi, utilise
l'adresse locale affichée par l'hôte. Pour jouer sur Internet, l'hôte doit généralement
rediriger le port TCP 45123 de sa box ; certains réseaux derrière un CGNAT empêchent la
connexion directe.

La connexion utilise TLS 1.2 ou supérieur pour chiffrer les decks, les cartes spéciales et les
actions pendant leur transport. Le certificat temporaire de l'hôte est vérifié par empreinte,
et une preuve HMAC du code de partie authentifie l'invitation ; le code n'est pas envoyé tel quel.
Partage le code uniquement avec l'adversaire prévu : il sert de secret d'accès à la partie.
Les deux joueurs doivent utiliser une version de l'application compatible avec le même protocole.

Chaque joueur choisit exactement trois cartes. Une est tirée au hasard pour sa main initiale ;
les deux autres forment une pioche sans doublon. À son tour, le joueur réalise une action :
piocher une carte de personnage, piocher une carte spéciale, poser une carte, attaquer ou jouer
une carte spéciale ; passer son tour est également possible. Une carte spéciale piochée reste
dans la main tant que son joueur ne la joue pas ; la piocher puis la jouer consomme donc deux
tours. Une carte posée ne peut attaquer qu'à partir de son prochain tour. Une carte K.-O. n'est
pas automatiquement remplacée. Le joueur qui héberge commence.

La pioche spéciale est commune aux deux joueurs et contient exactement 11 cartes : 2 Médecins,
3 Secouristes, 2 Policiers, 1 SEK, 2 Modérateurs et 1 Admin. Une carte spéciale jouée part dans
la défausse commune ; aucune carte spéciale ne revient dans la pioche, qui n'est jamais
remélangée. Les pouvoirs ciblent uniquement des cartes vivantes déjà posées sur le terrain.
Le Médecin soigne jusqu'à 70 PV et le Secouriste jusqu'à 35 PV (sans dépasser 100 PV).
Le Policier bloque une carte ennemie et le SEK en bloque deux pendant les deux prochains tours
de leur propriétaire, les empêchant d'attaquer. Le Modérateur renvoie une carte ennemie dans
la pioche de son propriétaire pour qu'elle puisse être reposée. L'Admin retire définitivement
une carte ennemie de la partie.
Les illustrations locales du Médecin, du Secouriste, du Policier, du SEK et de l'Admin sont
affichées lorsqu'une carte spéciale est sélectionnée. Le Modérateur utilise provisoirement la
même illustration que l'Admin.

Le deck choisi (noms, catégories, attaques et PV) est transmis à l'adversaire
pour la partie ; les photos et le reste de la collection ne sont pas envoyés. Le joueur qui
lance Braquage Express choisit la carte adverse de la main qu'il défausse. Bullseye affecte
les six cartes des deux decks ; Aimbot peut cibler n'importe laquelle des cartes adverses non
défaussées.

L'identifiant joueur, conservé dans `%APPDATA%\CassosDeck\profile.json`, identifie le profil
local mais ne permet pas à lui seul de retrouver une personne. Ferme la fenêtre de partie pour
mettre fin à l'écoute du port.
