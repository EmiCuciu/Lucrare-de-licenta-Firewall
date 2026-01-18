# 🛡️ NetGuard: Sistem de Firewall și Interceptare Trafic de Pachete

> **Proiect de Licență** | 2026  
> Un sistem de management al firewall-ului pe Linux cu interceptare de pachete în timp real, analiză și vizualizare.


## 📖 Prezentare Generală

**NetGuard** este o soluție software creată pentru a face legătura între nivelul scăzut al kernel-ului Linux și managementul la nivel de utilizator. Spre deosebire de firewall-urile standard care aplică doar reguli statice, NetGuard folosește `NFQUEUE` pentru a intercepta pachetele, trimițându-le către un motor Python ("userspace") pentru inspecție profundă și luarea deciziilor în timp real.

**Obiective Principale:**
* Interceptarea pachetelor de rețea folosind `iptables`/`nftables`.
* Analiza metadatelor traficului în timp real.
* Logarea activității într-o bază de date locală.
* Oferirea unei interfețe Web (UI) pentru monitorizarea traficului și gestionarea regulilor de blocare.

## 🏗️ Arhitectură

Sistemul funcționează pe trei niveluri distincte:

1.  **Nivelul Rețea (Kernel Space):**
    * Folosește `iptables` pentru a capta pachetele în lanțurile `FORWARD` sau `INPUT`.
    * Redirecționează pachetele către o coadă userspace (`NFQUEUE num 1`).
2.  **Nivelul Logic (Userspace - Python):**
    * **Interceptor:** Ascultă coada și parsează pachetele folosind librăria `Scapy`.
    * **Motor de Decizie:** Verifică pachetele contra unei baze de date de reguli (ACCEPT/DROP).
    * **Logger:** Salvează statisticile de trafic în SQLite.
3.  **Nivelul Prezentare (Web UI):**
    * **Backend:** API Flask pentru a servi datele și a primi comenzi de configurare.
    * **Frontend:** Dashboard HTML/JS cu `Chart.js` pentru vizualizare grafică.

```mermaid
graph TD;
    A[Internet/Rețea] -->|Pachet| B(Linux Kernel / iptables);
    B -->|NFQUEUE| C{Interceptor Python};
    C -->|Analiză cu Scapy| D[Motor de Decizie];
    D -->|Logare| E[(Bază de Date SQLite)];
    D -->|Verdict: ACCEPT/DROP| B;
    F[Dashboard Web] <-->|REST API| G[Backend Flask];
    G <-->|Interogare/Actualizare| E;
