
<template lang="pug">
q-card
  q-card-section
    q-toolbar
      q-btn.full-width(icon="add", unelevated, @click="on_Neu('Zeitleiste')")
  q-card-section
    q-list.rounded-borders(bordered)
      q-expansion-item(
        v-for="(Zeitleiste, ZI) in Zeitleisten",
        :key="ZI",
        expand-separator,
        :label="Zeitleiste.Wann"
      )
        q-card
          q-card-section
            q-toolbar
              q-btn.full-width(
                icon="add",
                unelevated,
                @click="on_Neu('Eintrag', ZI)"
              )
              q-btn.full-width(
                icon="delete",
                unelevated,
                @click="on_Loeschen('Zeitleiste', ZI)"
              )
              q-btn.full-width(icon="save", unelevated)
          q-card-section
            q-input(filled, v-model="Zeitleiste.Wann", style="width: 300px")
              template(v-slot:prepend)
                q-icon.cursor-pointer(name="event")
                  q-popup-proxy(
                    transition-show="scale",
                    transition-hide="scale"
                  )
                    q-date(v-model="Zeitleiste.Wann", mask="DD.MM.YYYY HH:mm")
                      .row.items-center.justify-end
                        q-btn(
                          v-close-popup,
                          label="Fertig",
                          color="primary",
                          flat
                        )
              template(v-slot:append)
                q-icon.cursor-pointer(name="access_time")
                  q-popup-proxy(
                    transition-show="scale",
                    transition-hide="scale"
                  )
                    q-time(
                      v-model="Zeitleiste.Wann",
                      mask="DD.MM.YYYY HH:mm",
                      format24h
                    )
                      .row.items-center.justify-end
                        q-btn(
                          v-close-popup,
                          label="Fertig",
                          color="primary",
                          flat
                        )
        //-| {{ Zeitleiste }}

        q-timeline(dense, side="right", layout="comfortable")
          q-timeline-entry(
            v-for="(Eintrag, ZEI) in Zeitleiste.Inhalte",
            :key="ZEI",
            :title="Eintrag.Beschreibung",
            :subtitle="Eintrag.Was",
            icon="delete"
          )
            //-| {{ Eintrag }}
            q-toolbar
              q-btn.full-width(
                icon="arrow_upward",
                unelevated,
                v-if="ZEI !== 0",
                @click="on_verschieben(ZI, ZEI, 'hoch')"
              )
              q-btn.full-width(
                icon="arrow_downward",
                unelevated,
                v-if="ZEI !== Zeitleiste.Inhalte.length - 1",
                @click="on_verschieben(ZI, ZEI, 'runter')"
              )
              q-btn.full-width(
                icon="delete",
                unelevated,
                @click="on_Loeschen('Eintrag', ZI, ZEI)"
              )
            q-list
              q-expansion-item(
                expand-separator,
                label="Konfiguration",
                header-class="bg-primary text-white"
              )
                q-select(label="Typ", v-model="Eintrag.Was", :options="Typen")
                q-input(label="Beschreibung", v-model="Eintrag.Beschreibung")

                div(v-if="Eintrag.Was === 'Person'")
                  q-input(label="Person", v-model="Eintrag.Name")
                div(v-if="Eintrag.Was === 'Textwort'")
                  q-list(separator)
                    q-item
                      q-item-section
                        q-input(
                          label="Bezeichnung",
                          v-model="Eintrag.Bezeichnung"
                        )
                      q-item-section
                        q-select(
                          label="Buch",
                          v-model="Eintrag.Buch",
                          :options="Object.keys(Bibel)"
                        )
                      q-item-section
                        q-input(
                          label="Kapitel",
                          v-model.number="Eintrag.Kapitel",
                          type="number"
                        )
                      q-item-section
                        q-input(
                          label="Vers von",
                          v-model.number="Eintrag.Vers_von",
                          type="number"
                        )
                      q-item-section
                        q-input(
                          label="Vers bis",
                          v-model.number="Eintrag.Vers_bis",
                          type="number"
                        )
                div(v-if="Eintrag.Was === 'Strophe anzeigen'")
                  q-list
                    q-item
                      q-item-section
                        q-input(label="Liednummer", v-model="Eintrag.Nummer")
                      q-item-section
                        q-input(label="Strophe", v-model="Eintrag.Strophe")
                      q-item-section
                        q-select(
                          label="Typ",
                          v-model="Eintrag.Typ",
                          :options="['Eingangslied', 'Bußlied', 'Abendmahlslied', 'Schlusslied', 'ohne']"
                        )
                div(v-if="Eintrag.Was === 'Lied anzeigen'")
                  q-list
                    q-item
                      q-item-section
                        q-input(label="Nummer", v-model="Eintrag.Nummer")
                      q-item-section
                        q-select(
                          label="Typ",
                          v-model="Eintrag.Typ",
                          :options="['Eingangslied', 'Bußlied', 'Abendmahlslied', 'Schlusslied', 'ohne']"
                        )
                div(v-if="Eintrag.Was === 'Logo / Live'")
                  q-card
                    q-card-section
                      q-input(
                        label="Uhrzeit",
                        mask="time",
                        :rules="['time']",
                        v-model="Eintrag.Uhrzeit"
                      )
                div(v-if="Eintrag.Was === 'YouTube unsichtbar'")
                  q-card
                    q-card-section
                      q-input(
                        label="Uhrzeit",
                        mask="time",
                        :rules="['time']",
                        v-model="Eintrag.Uhrzeit"
                      )
                div(v-if="Eintrag.Was === 'YouTube sichtbar'")
                  q-card
                    q-card-section
                      q-input(
                        label="Uhrzeit",
                        mask="time",
                        :rules="['time']",
                        v-model="Eintrag.Uhrzeit"
                      )
                div(v-if="Eintrag.Was === 'Stream starten'")
                  q-card
                    q-card-section
                      q-input(
                        label="Uhrzeit",
                        mask="time",
                        :rules="['time']",
                        v-model="Eintrag.Uhrzeit"
                      )
                div(v-if="Eintrag.Was === 'Wartebildschirm sichtbar machen'")
                  q-card
                    q-card-section
                      q-select(
                        label="Gottesdienstart",
                        :options="['ohne', 'Gottesdienst', 'Jugendgottesdienst', 'Seniorengottesdienst', 'Kindergottesdienst', 'Bezirksgottesdienst']",
                        v-model="Eintrag.Art"
                      )
                    q-card-section
                      q-checkbox(
                        label="Datum anzeigen",
                        v-model="Eintrag.Datum"
                      )
                    q-card-section
                      q-checkbox(
                        label="Startzeit",
                        v-model="Eintrag.Startzeit"
                      )
                      div(v-if="Eintrag.Startzeit")
                        q-input(
                          label="Uhrzeit",
                          mask="time",
                          :rules="['time']",
                          v-model="Eintrag.Startuhrzeit"
                        )
                    q-card-section
                      q-checkbox(v-model="Eintrag.Hinweis") Hinweis anzeigen
                      div(v-if="Eintrag.Hinweis")
                        b Bitte den Text spätestens am rechten Rand durch Druck auf ENTER / RETURN umbrechen. Sonst läuft der Text aus dem Bild. Vorher kann ein Umbruch natürlich auch schon eingefügt werden werden
                        q-editor(
                          v-model="Eintrag.Hinweistext",
                          :toolbar="[]",
                          style="width: 500px"
                        )
</template>

<script>
export default {
  components: {},
  name: 'App',
  data: () => ({
    Typen: ['Wartebildschirm sichtbar machen', 'Automatik deaktivieren', 'Stream starten', 'Logo / Live', 'Lied anzeigen', 'Predigt', 'Strophe anzeigen', 'Textwort', 'Person', 'Ende der Übertragung', 'Stream beenden', 'YouTube privat', 'YouTube sichtbar', 'YouTube unsichtbar'],

    Bibel: {
      '1. Mose': 50,
      '2. Mose': 40,
      '3. Mose': 27,
      '4. Mose': 36,
      '5. Mose': 34,
      Josua: 24,
      Richter: 21,
      Rut: 4,
      '1. Samuel': 31,
      '2. Samuel': 24,
      '1. Könige': 22,
      '2. Könige': 25,
      '1. Chronik': 29,
      '2. Chronik': 36,
      Esra: 10,
      Nehemia: 13,
      Ester: 10,
      Hiob: 42,
      Psalm: 150,
      Sprüche: 31,
      Prediger: 12,
      Hohelied: 8,
      Jesaja: 66,
      Jeremia: 52,
      Klagelieder: 5,
      Hesekiel: 48,
      Daniel: 12,
      Hosea: 14,
      Joel: 4,
      Amos: 9,
      Obadja: 1,
      Jona: 4,
      Micha: 7,
      Nahum: 3,
      Habakuk: 3,
      Zefanja: 3,
      Haggai: 2,
      Sacharja: 14,
      Maleachi: 3,
      Matthäus: 28,
      Markus: 16,
      Lukas: 24,
      Johannes: 21,
      Apostelgeschichte: 28,
      Römer: 16,
      '1. Korinther': 16,
      '2. Korinther': 13,
      Galater: 6,
      Epheser: 6,
      Philipper: 4,
      Kolosser: 4,
      '1. Thessalonicher': 5,
      '2. Thessalonicher': 3,
      '1. Timotheus': 6,
      '2. Timotheus': 4,
      Titus: 3,
      Philemon: 1,
      Hebräer: 13,
      Jakobus: 5,
      '1. Petrus': 5,
      '2. Petrus': 3,
      '1. Johannes': 5,
      '2. Johannes': 1,
      '3. Johannes': 1,
      Judas: 1,
      Offenbarung: 22
    },
    Zeitleisten: [{
      Wann: '01.04.2021 10:00',
      Inhalte: [{
        Was: 'Wartebildschirm sichtbar machen',
        Art: 'Gottesdienst',
        Datum: true,
        Startuhrzeit: '',
        Startzeit: true,
        Hinweis: false,
        Hinweistext: '',
        Beschreibung: 'Wartebildschirm vor dem Start des Streams aktivieren'
      },
      {
        Was: 'Stream starten',
        Uhrzeit: '09:15',
        Beschreibung: ''
      },
      {
        Was: 'Logo / Live',
        Uhrzeit: '09:45',
        Beschreibung: ''
      },
      {
        Was: 'YouTube sichtbar',
        Uhrzeit: '09:45',
        Beschreibung: 'Schaltet die Übertragung auf YouTube für alle sichtbar'
      },
      {
        Was: 'Lied anzeigen',
        Nummer: '2',
        Typ: 'Eingangslied',
        Beschreibung: ''
      },
      {
        Was: 'Predigt',
        Beschreibung: 'Ansagen vor dem Gottesdienst'
      },
      {
        Was: 'Lied anzeigen',
        Nummer: '2',
        Typ: 'Eingangslied',
        Beschreibung: ''
      },
      {
        Was: 'Strophe anzeigen',
        Nummer: '2',
        Typ: 'Eingangslied',
        Strophe: 1,
        Beschreibung: ''
      },
      {
        Was: 'Strophe anzeigen',
        Nummer: '2',
        Typ: 'Eingangslied',
        Strophe: 2,
        Beschreibung: ''
      },
      {
        Was: 'YouTube unsichtbar',
        Uhrzeit: '10:05',
        Beschreibung: 'Schaltet die Übertragung auf YouTube für alle sichtbar'
      },
      {
        Was: 'Automatik deaktivieren',
        Uhrzeit: '10:06',
        Beschreibung: 'Automatische Hintergrundverarbeitung deaktivieren'
      },
      {
        Was: 'Strophe anzeigen',
        Nummer: '2',
        Typ: 'Eingangslied',
        Strophe: 3,
        Beschreibung: ''
      },
      {
        Was: 'Predigt',
        Beschreibung: 'Textwort'
      },
      {
        Was: 'Textwort',
        Buch: '1. Mose',
        Kapitel: 2,
        Vers_von: 1,
        Vers_bis: 2,
        Beschreibung: ''
      },
      {
        Was: 'Predigt',
        Beschreibung: 'Textwort ggf. wieder ausblenden'
      },
      {
        Was: 'Musik',
        Beschreibung: 'Lied nach Textwort'
      },
      {
        Was: 'Predigt',
        Beschreibung: 'Predigt nach Textwort'
      },
      {
        Was: 'Person',
        Name: 'Kaufmann, Olaf',
        Beschreibung: 'Dienstleiter'
      },
      {
        Was: 'Predigt',
        Beschreibung: 'Name wieder ausblenden'
      },
      {
        Was: 'Musik',
        Beschreibung: 'Lied zum Wechsel'
      },
      {
        Was: 'Predigt',
        Beschreibung: 'Predigtzugabe'
      },
      { Was: 'Person', Name: 'Schröder, Florian', Beschreibung: 'Zugabe' },
      {
        Was: 'Predigt',
        Beschreibung: 'Name Zugabe wieder ausblenden'
      },
      { Was: 'Strophe anzeigen', Nummer: '23', Typ: 'Bußlied', Strophe: 2, Beschreibung: '' },
      { Was: 'Strophe anzeigen', Nummer: '48', Typ: 'Abendmahlslied', Strophe: 1, Beschreibung: '' },
      { Was: 'Strophe anzeigen', Nummer: '48', Typ: 'Abendmahlslied', Strophe: 2, Beschreibung: '' },
      { Was: 'Strophe anzeigen', Nummer: '48', Typ: 'Abendmahlslied', Strophe: 3, Beschreibung: '' },
      { Was: 'Strophe anzeigen', Nummer: '48', Typ: 'Abendmahlslied', Strophe: 4, Beschreibung: '' },
      {
        Was: 'Predigt',
        Beschreibung: 'Nach dem Abendmahl'
      }, {
        Was: 'Musik',
        Beschreibung: 'Musikbeitrag um Abschluss'
      },
      {
        Was: 'Ende der Übertragung',
        Beschreibung: 'Hinweis einblenden, dass die Übertragung beendet ist'
      },
      {
        Was: 'Stream beenden',
        Beschreibung: 'Beendet den Stream'
      },
      {
        Was: 'YouTube privat',
        Beschreibung: 'Schaltet die Übertragung auf YouTube auf privat'
      }
      ]
    }]
  }),
  methods: {
    on_Loeschen (Was, Zeitleiste, Index = null) {
      this.$q.dialog({
        title: 'Wirklich löschen?',
        message: 'Möchtest Du diesen Eintrag wirklich löschen?',
        cancel: true,
        persistent: true
      }).onOk(() => {
        switch (Was) {
          case 'Zeitleiste':
            this.Zeitleisten.splice(Zeitleiste, 1)
            break
          case 'Eintrag':
            this.Zeitleisten[Zeitleiste].Inhalte.splice(Index, 1)
            break
        }
      })
    },
    on_Neu (Was, Zeitleiste = null) {
      switch (Was) {
        case 'Zeitleiste':
          this.$q.dialog({
            title: 'Neue Zeitleiste',
            message: 'Was für eine Zeitleiste möchtest Du erstellen?',
            options: {
              type: 'radio',
              model: 'opt1',
              // inline: true
              items: [
                { label: 'Vorlage', value: 'Vorlage' },
                { label: 'leer', value: 'leer' }
              ]
            },
            cancel: true,
            persistent: true
          }).onOk(Auswahl => {
            if (Auswahl === 'Vorlage') {
              this.Zeitleisten.push(this.Vorlage())
            } else {
              this.Zeitleisten.push({ Wann: '01.01.2021 00:00', Inhalte: [] })
            }
          })
          break
        case 'Eintrag':
          this.Zeitleisten[Zeitleiste].Inhalte.push({
            Was: '',
            Beschreibung: ''
          })
          break
      }
    },
    on_verschieben (Zeitleiste, Von, Richtung) {
      let lintNeuerIndex
      if (Richtung === 'hoch') {
        lintNeuerIndex = Von - 1
      } else {
        lintNeuerIndex = Von + 1
      }
      var Element = this.Zeitleisten[Zeitleiste].Inhalte[Von]
      this.Zeitleisten[Zeitleiste].Inhalte.splice(Von, 1)
      this.Zeitleisten[Zeitleiste].Inhalte.splice(lintNeuerIndex, 0, Element)
    },
    Vorlage () {
      return {
        Wann: '01.01.2021 00:00',
        Inhalte: [{
          Was: 'Wartebildschirm sichtbar machen',
          Art: 'Gottesdienst',
          Datum: true,
          Startuhrzeit: '',
          Startzeit: true,
          Hinweis: false,
          Hinweistext: '',
          Beschreibung: 'Wartebildschirm vor dem Start des Streams aktivieren'
        },
        {
          Was: 'Stream starten',
          Uhrzeit: '09:15',
          Beschreibung: ''
        },
        {
          Was: 'Logo / Live',
          Uhrzeit: '09:45',
          Beschreibung: ''
        },
        {
          Was: 'YouTube sichtbar',
          Uhrzeit: '09:45',
          Beschreibung: 'Schaltet die Übertragung auf YouTube für alle sichtbar'
        },
        {
          Was: 'Lied anzeigen',
          Nummer: '2',
          Typ: 'Eingangslied',
          Beschreibung: ''
        },
        {
          Was: 'Predigt',
          Beschreibung: 'Ansagen vor dem Gottesdienst'
        },
        {
          Was: 'Lied anzeigen',
          Nummer: '2',
          Typ: 'Eingangslied',
          Beschreibung: ''
        },
        {
          Was: 'Strophe anzeigen',
          Nummer: '2',
          Typ: 'Eingangslied',
          Strophe: 1,
          Beschreibung: ''
        },
        {
          Was: 'Strophe anzeigen',
          Nummer: '2',
          Typ: 'Eingangslied',
          Strophe: 2,
          Beschreibung: ''
        },
        {
          Was: 'YouTube unsichtbar',
          Uhrzeit: '10:05',
          Beschreibung: 'Schaltet die Übertragung auf YouTube für alle sichtbar'
        },
        {
          Was: 'Automatik deaktivieren',
          Uhrzeit: '10:06',
          Beschreibung: 'Automatische Hintergrundverarbeitung deaktivieren'
        },
        {
          Was: 'Strophe anzeigen',
          Nummer: '2',
          Typ: 'Eingangslied',
          Strophe: 3,
          Beschreibung: ''
        },
        {
          Was: 'Predigt',
          Beschreibung: 'Textwort'
        },
        {
          Was: 'Textwort',
          Buch: '1. Mose',
          Kapitel: 2,
          Vers_von: 1,
          Vers_bis: 2,
          Beschreibung: ''
        },
        {
          Was: 'Predigt',
          Beschreibung: 'Textwort ggf. wieder ausblenden'
        },
        {
          Was: 'Musik',
          Beschreibung: 'Lied nach Textwort'
        },
        {
          Was: 'Predigt',
          Beschreibung: 'Predigt nach Textwort'
        },
        {
          Was: 'Person',
          Name: 'Kaufmann, Olaf',
          Beschreibung: 'Dienstleiter'
        },
        {
          Was: 'Predigt',
          Beschreibung: 'Name wieder ausblenden'
        },
        {
          Was: 'Musik',
          Beschreibung: 'Lied zum Wechsel'
        },
        {
          Was: 'Predigt',
          Beschreibung: 'Predigtzugabe'
        },
        { Was: 'Person', Name: 'Schröder, Florian', Beschreibung: 'Zugabe' },
        {
          Was: 'Predigt',
          Beschreibung: 'Name Zugabe wieder ausblenden'
        },
        { Was: 'Strophe anzeigen', Nummer: '23', Typ: 'Bußlied', Strophe: 2, Beschreibung: '' },
        { Was: 'Strophe anzeigen', Nummer: '48', Typ: 'Abendmahlslied', Strophe: 1, Beschreibung: '' },
        { Was: 'Strophe anzeigen', Nummer: '48', Typ: 'Abendmahlslied', Strophe: 2, Beschreibung: '' },
        { Was: 'Strophe anzeigen', Nummer: '48', Typ: 'Abendmahlslied', Strophe: 3, Beschreibung: '' },
        { Was: 'Strophe anzeigen', Nummer: '48', Typ: 'Abendmahlslied', Strophe: 4, Beschreibung: '' },
        {
          Was: 'Predigt',
          Beschreibung: 'Nach dem Abendmahl'
        }, {
          Was: 'Musik',
          Beschreibung: 'Musikbeitrag um Abschluss'
        },
        {
          Was: 'Ende der Übertragung',
          Beschreibung: 'Hinweis einblenden, dass die Übertragung beendet ist'
        },
        {
          Was: 'Stream beenden',
          Beschreibung: 'Beendet den Stream'
        },
        {
          Was: 'YouTube privat',
          Beschreibung: 'Schaltet die Übertragung auf YouTube auf privat'
        }
        ]
      }
    },
  }
}
</script>
