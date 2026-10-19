---
description: Melious einrichten — CLI, jcode und Schlüssel (Schlüssel über ein Formular, nie im Chat)
---
Richte das Melious-Plugin für diese Person ein:

1. Führe `"${CLAUDE_PLUGIN_ROOT}/bin/mel-setup"` im Hintergrund aus und lies die Ausgabe mit.
2. Sobald die Zeile `mel-key: Formular offen → <Link>` erscheint, zeige der Person **nur diesen Link** und sag in einem Satz:
   „Klick auf den Link, füge deinen Melious-Schlüssel ein und klick Speichern — ich warte.“
   Frag nie nach dem Schlüssel im Chat und gib ihn nie aus.
3. Wenn `Fertig ✓` erscheint, mach eine Probe mit `mel-sub -p "Sag Hallo"` und melde das Ergebnis in einem Satz.
   Steht irgendwo ✗, erkläre in einfachen Worten, was fehlt, und was du als Nächstes tust.
