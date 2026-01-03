# Space Pinball: Physics-Driven Engine

[![Python](https://img.shields.io/badge/Python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![Pygame](https://img.shields.io/badge/Library-Pygame-green.svg)](https://www.pygame.org/)
[![License-MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

**Space Pinball** je napredna 2D simulacija flipera implementirana u Pythonu. Fokus projekta je na preciznom modeliranju fizike kretanja krutih tela, detekciji kolizija složenih poligona i dinamičkoj vizuelizaciji u realnom vremenu.

> *Aplikacija je razvijena kao univerzitetski projekat, ali je u potpunosti funkcionalna i spremna za upotrebu.*

---

## Ključne Karakteristike
- **Dinamička fizika:** Implementacija kretanja zasnovana na Ojlerovim jednačinama.
- **Napredna detekcija kolizija:** Korišćenje *Separating Axis Theorem (SAT)* za interakciju sa poligonima.
- **Vektorska refleksija:** Realistično odbijanje loptice od zakrivljenih i ravnih površina.
- **Responzivni UI:** Automatsko skaliranje elemenata igre u odnosu na rezoluciju ekrana korisnika.
- **Svemirska estetika:** Tematski prilagođeni sprite-ovi (planete, zvezde, Yoda hexagon).

---

## Tehnička Implementacija

### 1. Kinematika i Integracija
Kretanje loptice se ne oslanja na prosto pomeranje koordinata, već na fizički model:
- **Ojlerova Metoda:** Koristi se za numeričku integraciju brzine i pozicije u zavisnosti od vremena (`dt`).
- **Gravitacioni Model:** Sila gravitacije je dekomponovana na vektorske komponente na osnovu nagiba table ($\alpha = 30^\circ$).
- **Rotacija:** Ugaona brzina ($\omega$) je direktno povezana sa linearnom brzinom i poluprečnikom loptice ($v = \omega \cdot r$).

### 2. Algoritmi Kolizije
U igri su implementirana tri nivoa detekcije:
*   **Circle-Circle:** Geometrijska provera rastojanja između centara planeta i loptice.
*   **Line-Circle:** Projekcija vektora pozicije na normalu linije za precizno odbijanje.
*   **SAT (Separating Axis Theorem):** Robustan algoritam koji omogućava loptici da detektuje sudare sa konveksnim poligonima (trapezoidi i krilca) proverom preklapanja na svim osama normale.

### 3. Vektorska Matematika
Sve interakcije koriste `pygame.math.Vector2` za:
- Izračunavanje upadnih i odraznih uglova ($R = I - 2(I \cdot n)n$).
- Normalizaciju vektora smera radi očuvanja impulsa.

---

## Kontrole i bodovanje

| Komanda | Akcija |
| :--- | :--- |
| `SPACE` | Lansiranje loptice (inicijalna sila) |
| `←` / `→` | Upravljanje levim i desnim krilcem (flipperima) |
| `ESC` | Izlaz iz igre |

**Sistem poena:**
- 🪐 **Planete:** 30 pts
- 🔷 **Trapezoidi:** 10 pts
- 🌌 **Yoda Hexagon:** 60 pts

---

## Sistemski preduslovi

Za uspešno izvršavanje simulacije neophodno je sledeće okruženje:
- **Runtime:** Python 3.11 ili novija verzija.
- **Dependencies:** `pygame` biblioteka (zadužena za rendering i event handling).
- **Zavisnosti operativnog sistema:** `Tkinter` (standardno uz Python na Windowsu, na Linuxu može zahtevati `python3-tk`).
- **Resursi:** Svi grafički aseti (`.png`) moraju biti u korenom direktorijumu projekta.
