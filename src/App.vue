<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/mission-icon.svg" />
        <h1>Mission Log</h1>
      </div>
      <div class="section-content-container">
        <h3>Current Assignment</h3>
        <Markdown :source="current_md" class="markdown" />
        <h3>Mission List</h3>
        <div class="mission-list-container">
          <Mission
            v-for="item in this.missions"
            :key="item.slug"
            :mission="item"
            :selected="this.mission_slug"
            @click="selectMission(item)"
          />
        </div>
      </div>
    </section>
    <section class="section-container" id="events" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Events Log</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="width:894px; height:714px;">
      <div style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img src="/icons/pilot-icon.svg" />
          <h1>Pilot Roster</h1>
        </div>
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <div class="pilot-list-container">
          <Pilot v-for="item in this.pilots" :key="item.slug" :pilot="item" />
        </div>
      </div>
    </section>
  </div>
  <svg
    style="visibility: hidden; position: absolute;"
    width="0"
    height="0"
    xmlns="http://www.w3.org/2000/svg"
    version="1.1"
  >
    <defs>
      <filter id="round">
        <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur" />
        <feColorMatrix
          in="blur"
          mode="matrix"
          values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -5"
          result="goo"
        />
        <feComposite in="SourceGraphic" in2="goo" operator="atop" />
      </filter>
    </defs>
  </svg>
  <audio autoplay>
    <source src="/startup.ogg" type="audio/ogg" />
  </audio>
  <Footer/>
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import Mission from './components/Mission.vue';
import Pilot from './components/Pilot.vue';
import Markdown from 'vue3-markdown-it';

export default {
  components: {
    Header,
    Footer,
    Mission,
    Pilot,
    Markdown
  },

  data() {
    return {
      "mission_slug": "001",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "001",
          "name": "Beach-Head",
          "status": "partial-success"
        },
        {
        "slug": "002",
          "name": "Strike and Seize",
          "status": "failure"
        },
        {
        "slug": "003",
          "name": "Revelations",
          "status": "start"
        },

      ],
      "pilots": [
        {
          "callsign": "Archimedes",
          "alias": "Jakar O'Rielly",
          "code": "f4489495-4818-4533-9662-7656a6b987f7///EOS-A-PEREGRINE-STATION",
          "corpro": "HA",
          "frame": "Iskandar",
          "mech": "Da Vinci"
        },
        {
          "callsign": "Barrelman",
          "alias": "Aleksandra",
          "code": "08aa7969-336e-41f7-887b-f6de15fb2611///EOS-A-PEREGRINE-STATION",
          "corpro": "SSC",
          "frame": "Oleander",
          "mech": "SPYGLASS Mk. II"
        },
        {
          "callsign": "Nine",
          "alias": "John Smith",
          "code": "13f8ca43-0278-49f6-a436-0fadf99344a2///EOS-A-PEREGRINE-STATION",
          "corpro": "IPS-N",
          "frame": "Caliban",
          "mech": "Stray"
        },
        {
          "callsign": "Letariat",
          "alias": '???',
          "code": "be49aa5b-f040-424e-b1ac-ebfeffed3200///EOS-A-PEREGRINE-STATION",
          "corpro": "HORUS",
          "frame": "Calendula",
          "mech": "Liberty Prime"
        },
        {
          "callsign": "Isador",
          "alias": '???',
          "code": "442a4c73-0ede-45c2-8dc6-411bc12751b3///EOS-A-PEREGRINE-STATION",
          "corpro": "SSC",
          "frame": "Metalmark",
          "mech": "MOTHER"
        },
      ],
      "header": {
        "planet": "Earth",
        "year": "2103",
        "system": "Sol",
        "gate": "Mars-Interchange",
        "ring": "Inner Ring",
        "headerTitle": "Scourge Brigade",
        "headerSubtitle": "LLC",
        "subheaderTitle": "Independent Contractor",
        "subheaderSubtitle": "HELIOS"
      },
      "options":{
        "eventsMarkdownPerMission": true
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {

  },

  methods: {
    selectMission(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if(this.options.eventsMarkdownPerMission){
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `/missions/${self.mission_slug}.md`
      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.current_md = client.responseText;
      }
      client.send();
    },
    loadEventsMarkdown() {
      let self = this;
      let md = "";

      if(self.options.eventsMarkdownPerMission){
        md = `/events/${self.mission_slug}.md`
      }
      else {
        md = "/events.md"
      }

      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      }
      client.send();
    }
  }

}
</script>


<style lang="scss">
#app {
  width: 1902px;
  height: 910px;
  overflow: hidden;
}
</style>
