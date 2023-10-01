<template>
  <Header :header="this.header" />
  <div class="content-container">
    <MissionView
      :missions="this.missions"
      :missionMarkdown="this.current_md"
      :selected="this.mission_slug"
      v-on:missionSelected="handleMissionSelected"
    />

    <EventsView :events="this.events" />
    <section class="section-container" id="main-tab">
      <div class="main-tab-header" style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img :src="mainTabIcon" />
          <h1>{{ mainTabTitle }}</h1>
        </div>
        <TabButton
          v-for="item in this.options.panelOptions"
          :name="item"
          :hidden="item === this.options.mainPanel"
          :key="item"
          @click="selectMainPanel(item)"
        />
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <transition name="fade" mode="out-in">
          <PilotsView v-if="this.options.mainPanel === 'pilot'" :pilots="this.pilots" />
          <NPCView v-else-if="this.options.mainPanel === 'npc'" :npcs="this.npcs" />
          <GlossaryView v-else-if="this.options.mainPanel === 'glossary'" />
          <ClocksView v-else-if="this.options.mainPanel === 'clock'" :clocks="this.clocks" />
        </transition>
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
  <Footer />
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import MissionView from './components/layout/MissionView.vue';
import EventsView from './components/layout/EventsView.vue';
import PilotsView from './components/layout/PilotsView.vue';
import NPCView from './components/layout/NPCView.vue';
import GlossaryView from './components/layout/GlossaryView.vue';
import TabButton from './components/TabButton.vue'

export default {
  components: {
    Header,
    Footer,
    MissionView,
    EventsView,
    PilotsView,
    NPCView,
    GlossaryView,
    TabButton,
  },

  data() {
    return {
      "mission_slug": "003",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "001",
          "name": "Bug-Hunt",
          "status": "success"
        },
        {
          "slug": "002",
          "name": "Vigilant Gaze",
          "status": "success"
        },
        {
          "slug": "003",
          "name": "Floodgate",
          "status": "success"
        },
        {
          "slug": "004",
          "name": "Double Harmony",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "Herkules",
          "alias": "Kanika Bayala",
          "code": "23c6e286-0c46-483a-81b1-63afcd76601d//NDL-C-OMEGA-KINGMAKER",
          "corpro": "Harrison Armory",
          "frame": "Tokugawa",
          "mech": "Last Word"
        },
        {
          "callsign": "𓂀",
          "alias": "Madoc VII",
          "code": "457058b5-10b0-4c01-8c49-30acfa595be2//NDL-C-LUNAR-LASH",
          "corpro": "Horus",
          "frame": "Manticore",
          "mech": "Hand of Horus"
        },
        {
          "callsign": "Raincaller",
          "alias": "Markus Wright",
          "code": "826968b0-6ea2-448a-858e-0869243516f7//NDL-C-SIGMA-JUDGE",
          "corpro": "Smith-Shimano Corpro",
          "frame": "Monarch",
          "mech": "Torrent"
        },
        {
          "callsign": "Seawolf※",
          "alias": "Caspian Blake",
          "code": "7d7ee47e-76fb-4d05-b875-566ba87bae62//NDL-C-STOLEN-KINGMAKER",
          "corpro": "IPS-N",
          "frame": "Blackbeard",
          "mech": "Foolish Wildcard"
        },
      ],
      "npcs": [
        {
          "name": "Lucculan",
          "affiliation": "Mirrorsmole Mercernary Company",
          "pronouns": "She/Her",
          "notes": "MSMC officer"
        },
        {
          "name": "Brava Hadura",
          "affiliation": "Evergreen",
          "pronouns": "She/Her",
          "notes": "Captain of the Local Militia"
        },
        {
          "name": "Patience",
          "affiliation": "Evergreen",
          "pronouns": "They/Them",
          "notes": "Colonial Administrator"
        },
        {
          "name": "Edena Ji",
          "affiliation": "Evergreen",
          "pronouns": "She/Her",
          "notes": "Chief Operations Officer, Attaché to Patience"
        },
      ],
      "header": {
        "planet": "Hercynia",
        "year": "5014u",
        "system": "Ardennes-3",
        "gate": "Atlas-Quanokrim",
        "ring": "Atlas-Line",
        "headerTitle": "Mirrorsmoke",
        "headerSubtitle": "Mercenary Company",
        "subheaderTitle": "Crisis Response",
        "subheaderSubtitle": "Shamsir Squadron",
      },
      "options": {
        "eventsMarkdownPerMission": true,
        "mainPanel": "pilot",
        "panelOptions": [
          "pilot",
          "npc",
          "glossary",
        ]
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {
    mainTabTitle() {
      if (this.options.mainPanel === "pilot") return "Pilot Roster"
      if (this.options.mainPanel === "npc") return "Persons Registry"
      if (this.options.mainPanel === "glossary") return "Lexicon"
    },
    mainTabIcon() {
      return `/icons/${this.options.mainPanel}-icon.svg`
    }
  },

  methods: {
    handleMissionSelected(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if (this.options.eventsMarkdownPerMission) {
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

      if (self.options.eventsMarkdownPerMission) {
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
    },
    selectMainPanel(panel) {
      this.options.mainPanel = panel;
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
