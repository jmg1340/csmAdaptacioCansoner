<template>
  <div class="q-pa-md">
    <div class="row q-gutter-x-sm">
      <div class="col">
        <q-card>
          <q-card-section class="row">
            <q-input
              class="col"
              v-model="idxInici"
              type="number"
              dense
            ></q-input>
            <q-input
              class="col"
              v-model="idxFinal"
              type="number"
              dense
            ></q-input>
          </q-card-section>
          <q-card-section>
            <div
              v-for="(obj, index) in rangCantoral"
              :key="'c' + index"
              class="row q-mb-xs"
            >
              <div class="col bg-yellow-2">
                [{{ obj.indexCantoral }}] {{ obj.linia }}
              </div>
              <div class="col bg-yellow-2">
                <q-btn
                  label="Titol"
                  color="primary"
                  dense
                  flat
                  @click="trasllatInfo('titol', obj.indexCantoral, obj.linia)"
                ></q-btn>
                <q-btn
                  label="Estrofa"
                  color="tertiary"
                  dense
                  flat
                  @click="trasllatInfo('estrofa', obj.indexCantoral, obj.linia)"
                ></q-btn>
                <q-btn
                  label="Tornada"
                  color="negative"
                  dense
                  flat
                  @click="trasllatInfo('tornada', obj.indexCantoral, obj.linia)"
                ></q-btn>
              </div>
              <q-separator inset></q-separator>
            </div>
          </q-card-section>
        </q-card>
      </div>

      <div class="col">
        <div class="column q-gutter-y-sm q-pa-sm">
          <q-btn
            dense
            label="Nova cançó"
            @click="novaCanso"
            color="secondary"
          />

          <q-card class="col q-pa-sm">
            Titol: <span class="text-primary">{{ titol }}</span>
          </q-card>
          <q-card class="col q-pa-sm">
            Numero: <span class="text-primary">{{ numero }}</span>
          </q-card>
          <q-card class="col q-pa-sm">
            <div class="row">
              <div class="col">Paragraf</div>
              <div class="co">
                <q-btn
                  label="afegir"
                  color="warning"
                  dense
                  @click="afegirParagraf"
                />
              </div>
            </div>

            <q-select
              :options="[
                { label: 'Estrofa', value: 'estrofa' },
                { label: 'Tornada', value: 'tornada' },
              ]"
              label="Tipus"
              v-model="tipus"
              class="bg-red-1"
              dense
            />
            <div
              v-for="(linia, index2) in paragraf"
              :key="index2"
              class="text-primary"
            >
              <q-btn icon="delete" dense @click="eliminarLinia(index2)" />
              {{ linia }}
            </div>
          </q-card>
        </div>

        <div>
          <q-btn label="Copiar" noCaps color="negative" dense @click="copiar" />
        </div>

        <div>
          <pre>
            {{ objCanso }}
          </pre>
        </div>

        <div>
          <q-btn label="Copiar" noCaps color="negative" dense @click="copiar" />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { defineComponent, ref, computed } from "vue";
import { copyToClipboard } from "quasar";
import cantoral from "../cansoner/cantoral";

let idxInici = ref(5315);
let idxFinal = ref(idxInici.value + 11);

let rangCantoral = computed(() => {
  return cantoral
    .map((linia, index) => {
      return {
        indexCantoral: index,
        linia: linia,
      };
    })
    .filter((obj, idx) => {
      return idx >= idxInici.value && idx <= idxFinal.value;
    });
});

const trasllatInfo = (categoria, index, linia) => {
  switch (categoria) {
    case "titol":
      novaCanso();

      const [numero2, titol2] = linia.split(".");

      titol.value = titol2.trim();
      numero.value = numero2.trim();

      // objCanso.value.titol = titol2
      objCanso.value[numero.value] = { titol: titol.value };
      break;
    case "tornada":
      tipus.value = { label: "Tornada", value: "tornada" };
      paragraf.value.push(linia);
      break;
    case "estrofa":
      tipus.value = { label: "Estrofa", value: "estrofa" };
      paragraf.value.push(linia);
      break;
  }

  idxInici.value = index + 1;
  idxFinal.value = index + 11;
};

let titol = ref("");
let numero = ref("");
let paragraf = ref([]);
let tipus = ref({ label: "Estrofa", value: "estrofa" });

const novaCanso = () => {
  titol.value = "";
  numero.value = "";
  paragraf.value = [];
  objCanso.value = {};
};

const eliminarLinia = (index2) => {
  paragraf.value.splice(index2, 1);
};

let objCanso = ref({});
let objParagraf = ref({});
// let arrLletra = ref([])

const afegirParagraf = () => {
  if (objCanso.value[numero.value].lletra === undefined)
    objCanso.value[numero.value].lletra = [];

  objCanso.value[numero.value].lletra.push({
    tipus: tipus.value.value,
    paragraf: JSON.parse(JSON.stringify(paragraf.value)),
  });

  paragraf.value = [];
};

const copiar = () => {
  let strPRE = document.querySelector("pre").innerHTML.trim();
  // strPRE = strPRE.replace(/&amp;/g, "&")
  strPRE = strPRE.slice(2, strPRE.length - 2).trim() + ",";

  copyToClipboard(strPRE)
    .then(() => {
      // success!
    })
    .catch((error) => {
      console.log("error al copiar", error);
    });
};

// export default defineComponent({
//   name: 'IndexPage'
// })
</script>
