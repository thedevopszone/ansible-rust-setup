# Ansible Rust/eBPF Setup

Ansible-Playbook zur automatischen Einrichtung einer Rust-Entwicklungsumgebung mit eBPF-Tooling auf Ubuntu 24.04.

## Was wird installiert?

### System-Pakete
- `curl`, `build-essential`, `pkg-config`, `libssl-dev`
- `linux-tools-common` + `linux-tools-<kernel>` (perf, bpftool)

### Rust Toolchain
- **rustup** mit stable (default) und nightly Toolchain
- **rust-src** (nightly) -- benötigt für eBPF-Kompilierung

### Cargo Tools
- **bpf-linker** -- Linker für eBPF-Programme
- **cargo-generate** -- Projekt-Templates (z.B. aya-rs)

## Voraussetzungen

- Ansible auf dem lokalen Rechner (`brew install ansible` / `apt install ansible`)
- SSH-Zugang zur Ziel-VM (Key-basiert, User `ubuntu`)
- Ziel-VM mit **Ubuntu 24.04**

## Konfiguration

### Zielhost anpassen

In `inventory.ini` die IP-Adresse oder den Hostnamen der VM eintragen:

```ini
[rust_dev]
192.168.1.100
```

### SSH-User anpassen

In `ansible.cfg` den `remote_user` ändern, falls nicht `ubuntu`:

```ini
[defaults]
remote_user = mein_user
```

## Verwendung

### Playbook ausführen

```bash
ansible-playbook playbook.yml
```

### Nur Verifikation (prüfen ob alles installiert ist)

```bash
ansible-playbook playbook.yml --start-at-task="Rustup-Version ausgeben"
```

### Verbindung testen

```bash
ansible rust_dev -m ping
```

## Verifikation

Das Playbook prüft am Ende automatisch, ob alle Komponenten korrekt installiert sind:

- stable Toolchain als Default gesetzt
- nightly Toolchain vorhanden
- `bpf-linker` erreichbar
- `cargo-generate` erreichbar
- `bpftool` erreichbar

## Projektstruktur

```
.
├── ansible.cfg      # Ansible-Konfiguration
├── inventory.ini    # Zielhost(s)
├── playbook.yml     # Haupt-Playbook
└── README.md
```

## Hinweise

- Die Installation von `bpf-linker` kann 10-20 Minuten dauern (wird kompiliert).
- Das Playbook ist idempotent -- es kann beliebig oft ausgeführt werden, bereits installierte Komponenten werden übersprungen.
- `host_key_checking` ist in `ansible.cfg` deaktiviert, da dies typischerweise für Entwicklungs-VMs genutzt wird.
