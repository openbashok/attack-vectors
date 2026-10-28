# attack-vectors

> A living dictionary of real-world web attack vectors — the paths that bots and scanners
> actually probe — harvested from an internet-facing honeypot and **updated automatically**.

Mantenido por **[OpenBash](https://openbash.com)** · *Pentest Research Tools*

---

## Qué es esto

Esto **no** es una wordlist curada a mano ni copiada de otra. Es la lista de los **paths que
realmente nos piden los bots y scanners** en un honeypot HTTP/HTTPS expuesto a internet, que
registra cada request que recibe. Deduplicada y **ordenada por frecuencia** (los vectores más
comunes primero), se regenera **todos los días** con el tráfico más fresco.

En otras palabras: en vez de adivinar qué rutas prueban los atacantes, las medimos. El
resultado es un diccionario que refleja la **superficie de ataque web que está siendo barrida
hoy** en internet real.

## De dónde sale

```
internet (bots/scanners) ──▶ honeypot HTTP/HTTPS (OpenResty+Lua) ──▶ PostgreSQL
                                                                         │
                                            de-dup + orden por frecuencia │  (diario)
                                                                         ▼
                                                            este repositorio
```

El honeypot es el proyecto open source **[reverse-scann](https://github.com/openbashok/reverse-scann)**.
Captura el path **crudo** tal cual llega (preservando payloads como `..%2f`, `%2e%2e`, dobles
extensiones), excluye el tráfico de nuestra propia infraestructura, y de ahí se derivan estos
archivos. Un job programado los actualiza en este repo.

## Archivos

| Archivo | Qué contiene |
|---|---|
| [`wordlists/paths.txt`](wordlists/paths.txt) | **Todos** los paths únicos, con nombre de archivo, por frecuencia |
| [`wordlists/paths-common.txt`](wordlists/paths-common.txt) | Solo los vistos **≥ 3 veces** (filtra el ruido de una sola vez) |
| [`wordlists/files.txt`](wordlists/files.txt) | Solo **nombres de archivo** (último segmento) |
| [`wordlists/segments.txt`](wordlists/segments.txt) | Cada **segmento de path** (directorios + archivos) por separado |

Ordenados por frecuencia descendente. Sin barra inicial (listos para fuzzers).

## Usos

- **Fuzzing de contenido / descubrimiento de rutas** en pentests autorizados:
  ```sh
  ffuf -w paths-common.txt -u https://TARGET/FUZZ
  gobuster dir -u https://TARGET -w paths.txt
  feroxbuster -u https://TARGET -w paths-common.txt
  ```
- **Threat intelligence**: ver qué CVEs y productos están siendo barridos masivamente
  (hoy: fuga de `.env`/`.git`, `eval-stdin.php` de ThinkPHP, `_profiler` de Symfony, etc.).
- **Tuning de detección (blue team)**: alimentar reglas de WAF/IDS con los vectores reales.
- **Honeypots / deception**: saber qué rutas cebar para que un honeypot parezca un target real.

## ⚠️ Importante

- **Uso autorizado únicamente.** Esta es una herramienta **defensiva / de investigación**.
  Usá estas wordlists solo contra sistemas propios o con permiso explícito.
- **Datos derivados de tráfico de atacantes.** El contenido proviene de input no confiable
  (lo que los bots eligen pedir). Se filtra por frecuencia y se sanitiza, pero tratalo como
  lo que es: un reflejo del ruido hostil de internet, no una verdad curada.

## Licencia

Los datos se publican bajo **CC0 1.0** (dominio público): usalos libremente, sin atribución
requerida. Ver [`LICENSE`](LICENSE).
