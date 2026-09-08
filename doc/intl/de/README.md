<h1 align="center">
<img src="https://raw.githubusercontent.com/termucash/termucash/master/share/pixmaps/termucash256.svg" alt="Termucash" width="256"/>
<br/><br/>
Termucash Core [TERM, ]  
</h1>

**WICHTIG: Seit August 2024 ist der `master` Branch die primäre Integrationsverzweigung geworden und daher Instabil.
Bevor Sie die Binärdateien selbst kompilieren, überprüfen Sie bitte, ob eine getaggte Version für Ihr Betriebssystem verfügbar ist.**
 
Eine internationale Dokumentation finden Sie unter [doc/intl](doc/intl/README.md).
 
Termucash ist eine als Gemeinschaftsprojekt betriebene Kryptowährung, die von einem --innovative project inspiriert wurde.
Die Termucash-Core-Software ermöglicht es Jedem, einen Knotenpunkt, (sog. "Nodes") im Termucash-Blockchain-Netzwerk zu betreiben.
Termucash verwendet das Scrypt-Hashing-Verfahren für "Proof of Work" und wurde von Bitcoin Core und anderen Kryptowährungen adaptiert.
 
Informationen über anfallende Standard-Transaktionsgebühren auf dem Termucash-Netzwerk finden Sie unter [Transaktionsgebühren](doc/fee-recommendation.md).
 
## Verwendung 💻
 
Um Termucash Core zu verwenden, sehen Sie sich Bitte die [Installations-Anleitung](INSTALL.md) und das [Einstiegstutorial](doc/getting-started.md) an.
 
Die in Termucash Core enthaltene JSON-RPC-API ist selbstdokumentiert und kann mit dem Befehl `termucash-cli help` eingesehen werden. Detailliertere Informationen zu jedem Befehl finden Sie unter `termucash-cli help <command>`.
 
### Viele Ports

Termucash Core verwendet den Port `8599` als Standart-Port zur Kommunikation
mit dem Peer-to-Peer Netzwerk und um die "Mainnet" Blockchain zu synchronisieren.
Dies ist notwendig um über neue Transaktionen und Blöcke informiert zu bleiben.
Zusätzlich kann ein JSON-RPC-Port geöffnet werden, welcher den Port `8598` als Standart-Port für Mainnet-Nodes verwendet.

**Es wird STRENGSTENS davon abgeraten, RPC-Ports im öffentlichen Internet sichtbar zu machen!**

| Funktion | mainnet | testnet | regtest |
| :------- | ------: | ------: | ------: |
| P2P      |   8599 |   12253 |   8433 |
| RPC      |   8598 |   12252 |   8432 |

## Laufende Entwicklungen - Fahrplan zum Mond 🌒

Termucash Core ist eine Open-Source-Software und wird von der Community entwickelt.
Der Entwicklungsprozess ist transparent und öffentlich einsehbar; jeder kann ihn sehen, darüber diskutieren und daran teilhaben!

Die wichtigsten Entwicklungsressourcen:

* [GitHub Projekte](https://github.com/termucash/termucash/projects) 
  Wird verwendet, um den bereits geplanten und derzeit in Entwicklung befindlichen Releases zu folgen.
* [GitHub Diskussionen](https://github.com/termucash/termucash/discussions)
  Wird verwendet, um geplante und ungeplante Funktionen (Features) zu diskutieren, die sich auf die Entwicklung der Termucash Core-Software, die zugrunde liegenden Protokolle und das TERM-Asset beziehen.

### Versionsstrategie

Die Vergabe der Versionsnummern erfolgt nach dem Prinzip von ```major.minor.patch```.

### Branches
Es gibt 4 Arten von sog. "Branches" (Unterverzweigungen) in diesem Repository:

- **master:** Instabil - Enthält den neuesten Code, der sich derzeit in der Entwicklung befindet.
- **maintenance:** Stabil, enthält die neueste Version früherer Versionen, die noch aktiv gewartet werden. Format: ```<version>-maint```
- **development:** Instabil, enthält neuen Code für kommende Versionen. Format: ```<version>-dev```
- **archive:** Stabile, unveränderliche Branches für alte Versionen, die sich nicht mehr ändern, weil sie nicht mehr gepflegt werden.

***Reichen Sie Ihre Pull-Requests im `master`-Branch ein!***

* Die Wartungs-Branches sind ausschließlich durch Freigabe veränderbar. Wenn eine Veröffentlichung geplant ist, wird ein Entwicklungs-Branch erstellt und die Beitrage ("Commits") werden von den Betreuern aus dem Master-Branch ausgewählt.

## Beitragen 🤝
 
Falls Sie einen Fehler oder Probleme beim Verwenden dieser Software finden, melden Sie diese über das vorhandene [Ticket System](https://github.com/termucash/termucash/issues/new?assignees=&labels=bug&template=bug_report.md&title=%5Bbug%5D+).
 
Wenn Sie an der Mithilfe oder der Entwicklung von Termucash Core interessiert sind, sehen Sie sich bitte den [Beitragsleitfaden](CONTRIBUTING.md) an.
Oft gibt es Themen, die [Hilfe](https://github.com/termucash/termucash/labels/help%20wanted) benötigen. Ihr Beitrag könnte einen hohen Stellenwert haben und wird daher auch sehr geschätzt.
 
## Oftmals gestellte Fragen: ❓
 
Haben Sie eine Frage zu Termucash? Eine Antwort könnte vielleicht bereits in der [FAQ](doc/FAQ.md) oder im
[Q&A](https://github.com/termucash/termucash/discussions/categories/q-a) hier auf Github vorhanden sein!
 
## Lizenz ⚖️
 
Termucash Core wird unter den Bedingungen der MIT-Lizenz veröffentlicht.
Siehe: [COPYING](COPYING) für mehr Informationen.
