# SETUP | portfoliosite sebastiaan.viezee.nl

Statische portfoliosite, geen backend. Zelfde deploypatroon als het Atom-platform: GitHub-repo, git pull op de Hetzner-server met een read-only deploy key, nginx plus certbot.

## Bestanden

- `index.html` | homepagina: positionering, vier sterktes, vier projectkaarten, contact
- `cv.html` | het cv (kopie van `Efforts/Loopbaanproject/cv-sebastiaan-viezee-dwr.html`, met het telefoonnummer verwijderd voor publiek gebruik)
- `nginx-portfolio.conf` | server-block voor de site

Bron van waarheid voor het cv blijft het bestand in `Efforts/Loopbaanproject/`. Wijzigt het cv daar, kopieer het dan opnieuw hierheen en haal het telefoonnummer er weer uit (drie plekken: JSON-LD, header-contactregel, slotregel).

## Stap 1 | DNS

A-record aanmaken bij de DNS-beheerder van viezee.nl:

```
sebastiaan.viezee.nl  A  46.225.28.205
```

Controleer daarna met `dig sebastiaan.viezee.nl +short` dat het IP klopt voordat je certbot draait.

## Stap 2 | Repo en deploy key

Lokaal (niet vanuit een cloud-sessie, zie de vaste les over OneDrive en git):

```
cd Efforts/Code/portfolio
git init && git add -A && git commit -m "Portfoliosite fase 1"
# repo aanmaken op GitHub (sebastiaan-a11y/portfolio) en pushen
```

Op de server een read-only deploy key, zelfde werkwijze als bij `/opt/beleggingen`:

```
sudo mkdir -p /opt/portfolio
cd /opt/portfolio
git clone git@github.com:sebastiaan-a11y/portfolio.git .
```

Deploy daarna altijd: push naar GitHub, `git pull` op de server.

## Stap 3 | nginx

```
sudo cp /opt/portfolio/nginx-portfolio.conf /etc/nginx/sites-available/sebastiaan.viezee.nl
sudo ln -s /etc/nginx/sites-available/sebastiaan.viezee.nl /etc/nginx/sites-enabled/
sudo nginx -t && sudo systemctl reload nginx
```

## Stap 4 | HTTPS

```
sudo certbot --nginx -d sebastiaan.viezee.nl --non-interactive --redirect
```

Certbot voegt het 443-blok en de redirect toe aan de live config. Zet die volledige config daarna terug in `nginx-portfolio.conf` in de repo, zodat de repo-versie compleet blijft (de bekende valkuil van het Atom-platform).

## Stap 5 | Controle

- `https://sebastiaan.viezee.nl` toont de homepagina zonder login
- `https://sebastiaan.viezee.nl/cv.html` toont het cv, zonder telefoonnummer
- Printvoorbeeld van cv.html geeft vier nette A4-pagina's
- `atom.viezee.nl` blijft gewoon achter Basic Auth staan

## Na livegang

1. Link zetten in LinkedIn Uitgelicht
2. De open link in de cv-sectie "Gebouwd en geautomatiseerd" invullen in docx, pdf en de HTML-versies
3. Fase 2: case-study-pagina's per projectkaart
