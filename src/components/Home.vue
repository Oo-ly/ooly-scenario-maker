<template>
  <div class="card-body">
    <h1 class="mb-4">Scenario maker</h1>

    <form action="#">
      <h5>Général</h5>
      <Input name="ID" v-model="scenario.id" />
      <Input name="Name" v-model="scenario.name" />
      <hr />
      <h5>Oos</h5>
      <div class="form-group" v-for="(sentenceOo, index) in scenarioOos" :key="index">
        <select class="form-control" v-model="sentenceOo.ooId">
          <option v-for="oo in oos" :key="oo.id" :value="oo.id">{{ oo.name }}</option>
        </select>
      </div>
      <button class="btn btn-success" @click.prevent="addOo">Ajouter Oo</button>
      <hr />
      <h5>Phrases annexes</h5>
      <div class="form-group">
        <div class="card mb-2" v-for="(scenarioAnnexe, index) in scenarioAnnexes" :key="index">
          <div class="card-header d-flex align-items-center">
            {{ scenarioAnnexe.hash }}
            <i class="ml-auto fas fa-eye" v-if="scenarioAnnexe.visible" @click.prevent="scenarioAnnexe.visible = !scenarioAnnexe.visible"></i>
            <i class="ml-auto fas fa-eye-slash" v-if="!scenarioAnnexe.visible" @click.prevent="scenarioAnnexe.visible = !scenarioAnnexe.visible"></i>
          </div>
          <div :class="`card-body ${scenarioAnnexe.visible ? 'd-block' : 'd-none'}`">
            <Input name="Hash" v-model="scenarioAnnexe.hash" />
            <Input name="Path" v-model="scenarioAnnexe.path" />
            <label>Type</label>
            <select class="form-control mb-3" v-model="scenarioAnnexe.type">
              <option value="entry">Introduction</option>
              <option value="exit">Phrase de fin</option>
            </select>
            <label>Oo</label>
            <select class="form-control" v-model="scenarioAnnexe.ooId">
              <option v-for="oo in oos" :key="oo.id" :value="oo.id">{{ oo.name }}</option>
            </select>
          </div>
        </div>
      </div>
      <button class="btn btn-success" @click.prevent="addAnnexe">Ajouter phrase</button>
      <hr />
      <h5>Phrases du scénario</h5>
      <div class="form-group">
        <div class="card mb-2" v-for="(sentence, index) in scenarioSentences" :key="index">
          <div class="card-header d-flex align-items-center">
            {{ sentence.hash }}
            <i class="ml-auto fas fa-eye" v-if="sentence.visible" @click.prevent="sentence.visible = !sentence.visible"></i>
            <i class="ml-auto fas fa-eye-slash" v-if="!sentence.visible" @click.prevent="sentence.visible = !sentence.visible"></i>
          </div>
          <div :class="`card-body ${sentence.visible ? 'd-block' : 'd-none'}`">
            <Input name="Hash" v-model="sentence.hash" />
            <Input name="Path" v-model="sentence.path" />
            <Input name="Order" v-model="sentence.order" />
            <div class="row">
              <div class="col-6">
                <div class="form-check">
                  <label class="form-check-label">
                    <input class="form-check-input" type="checkbox" v-model="sentence.interaction" />
                    Interaction
                  </label>
                </div>
              </div>
            </div>
            <label>Oo</label>
            <select class="form-control" v-model="sentence.ooId">
              <option v-for="oo in oos" :key="oo.id" :value="oo.id">{{ oo.name }}</option>
            </select>
          </div>
        </div>
      </div>
      <button class="btn btn-success" @click.prevent="addSentence">Ajouter phrase</button>
      <hr />
      <div class="form-group">
        <label for="scenarioJson">Scenario</label>
        <textarea class="form-control" name="scenarioJson" id="scenarioJson" v-model="scenarioJson"></textarea>
      </div>
      <div class="form-group">
        <label for="scenarioOosJson">Scenario Oos</label>
        <textarea class="form-control" name="scenarioOosJson" id="scenarioOosJson" v-model="scenarioOosJson"></textarea>
      </div>
      <div class="form-group">
        <label for="scenarioSentencesJson">Scenario Sentences</label>
        <textarea class="form-control" name="scenarioSentencesJson" id="scenarioSentencesJson" v-model="scenarioSentencesJson"></textarea>
      </div>
      <div class="form-group">
        <label for="audios">Audios</label>
        <textarea class="form-control" name="audios" id="audios" v-model="audios"></textarea>
      </div>
    </form>
  </div>
</template>

<script>
import Input from './Input';

export default {
  name: 'Home',
  components: { Input },
  data: () => {
    return {
      scenario: {
        id: 1,
        name: '',
      },
      scenarioOos: [],
      scenarioAnnexes: [],
      scenarioAudios: [],
      scenarioSentences: [],
      oos: [
        {
          id: 1,
          name: "Disc'Oo",
        },
        {
          id: 2,
          name: "Cin'Oo'che",
        },
        {
          id: 3,
          name: "Inf'Oo",
        },
        {
          id: 4,
          name: "Y'Oo'ga",
        },
        {
          id: 5,
          name: "Végét'Oo",
        },
        {
          id: 6,
          name: "Wh'Oo'w",
        },
        {
          id: 7,
          name: "C'Oo'mique",
        },
      ],
    };
  },
  computed: {
    scenarioJson: {
      get: function() {
        return JSON.stringify(this.scenario, undefined, 2);
      },
    },
    scenarioOosJson: {
      get: function() {
        return JSON.stringify(this.scenarioOos, undefined, 2);
      },
    },
    scenarioSentencesJson: {
      get: function() {
        const sentences = [];

        this.scenarioSentences.forEach((sentence) => {
          sentences.push({
            hash: sentence.hash,
            interaction: sentence.interaction,
            scenarioId: this.scenario.id,
          });
        });

        return JSON.stringify(sentences, undefined, 2);
      },
    },
    audios: {
      get: function() {
        const audios = [];
        this.scenarioAnnexes.forEach((annexe) => {
          audios.push({
            hash: annexe.hash,
            url: annexe.path,
            type: annexe.type,
            audibleId: this.scenario.id,
            audibleType: 'scenario',
            ooId: annexe.ooId,
          });
        });

        this.scenarioSentences.forEach((sentence, index) => {
          audios.push({
            hash: sentence.hash,
            url: sentence.path,
            type: sentence.type,
            audibleId: index + 1,
            audibleType: 'sentence',
            ooId: sentence.ooId,
          });
        });

        return JSON.stringify(audios, undefined, 2);
      },
    },
  },
  methods: {
    addOo() {
      this.scenarioOos.push({
        scenarioId: this.scenario.id,
        ooId: null,
      });
    },
    addAnnexe() {
      this.scenarioAnnexes.push({
        hash: null,
        path: null,
        type: null,
        ooId: null,
        visible: true,
      });
    },
    addSentence() {
      this.scenarioSentences.push({
        hash: null,
        path: null,
        interaction: false,
        type: null,
        ooId: null,
        order: this.scenarioSentences.length,
        visible: true,
      });
    },
  },
};
</script>

<style lang="scss" scoped></style>
