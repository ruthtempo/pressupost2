<template>
  <b-row>
     <b-col cols="8">
      <router-link to="/" class="backButton">Volver</router-link>
      <form id="formBox" @submit.prevent>
        <div class="formContent">
          <label>
            Nombre del Presupuesto
            <input type="text" v-model="nombre" />
          </label>
          <label>
            Cliente
            <input type="text" v-model="client" />
          </label>
          <h3>¿Qué quieres hacer?</h3>
          <div :key="index" v-for="(service, index) in services">
            <label>
              <input
                class="checkbox"
                type="checkbox"
                v-bind:value="index"
                v-model="selection"
              />
              {{ service.text }}: {{ service.value }} €
            </label>
            <Panell
              v-if="index === 0 && selection.includes(0)"
              @onchangepages="updatePages"
              @onchangelanguages="updateLanguages"
              :service-index="index"
              :pages="pages"
              :languages="languages"
            />
          </div>
          <div>
            <h3>Total: {{ getTotal() }}</h3>
          </div>
          <input class="saveButton" type="submit" value="Guardar" @click="saveList"/>
        </div>
      </form>
     </b-col>
      <b-col cols="4">
        <List 
        v-if="savedBudgetList.length > 0" 
        :budgetList="savedBudgetList" 
        @onClickDelete="updateList"
      />
      </b-col>
  </b-row>
</template>

<script>
import Panell from "./Panell.vue";
import List from "./PressupostList.vue";

function  updateQuery(){
  this.$router.replace({
    query:{
      pages: this.pages,
      languages: this.languages,
      pagina: this.selection.includes(0),
      consultoria: this.selection.includes(1),
      campaign: this.selection.includes(2),
    }
  })
}

export default {
  name: "Form",
  components: {
    Panell,
    List,
  },
  mounted(){
    this.pages = parseInt(this.$route.query.pages ?? 1)
    this.languages = parseInt(this.$route.query.languages ?? 1)
    
    if(this.$route.query.pagina === "true"){
      this.selection.push(0)
    };

     if(this.$route.query.consultoria === "true"){
      this.selection.push(1)
    };

     if(this.$route.query.campaign === "true"){
      this.selection.push(2)
    };

    // if it equals true in query , it checks the corresponding checkbox (push index into selection)
    
  },
  data() {
    return {
      selection: [], //array of service indexes
      services: [
        { text: "Página Web", value: 500 },
        { text: "Consultoría SEO", value: 300 },
        { text: "Campaña de Google Ads", value: 100 },
      ],
      pages: 1,
      languages: 1,
      savedBudgetList:[],
      client:"",
      nombre:"",
    };
  },
  watch: {
    pages: updateQuery,
    languages: updateQuery,
    selection:updateQuery,
  },
  methods: {
    getTotal() {
      let total = 0;
      // this.selection.forEach((service)=>{
      //   total += service.value
      // })

      this.selection.forEach((serviceIndex) => {
        total += this.services[serviceIndex].value;
        // (this.pages ?? 1) * // default value 1 so that we avoid multiplication with undefined
      });
      // for(let i=0; i< this.services.length; i++){
      //     total += this.selection.includes(i) ? this.services[i].value : 0;
      // }
      //   for(let i=0; i< this.selection.length; i++){
      //      total += this.services[this.selection[i]].value
      //   }

      if (this.selection.includes(0)) {
        return (total += this.pages * this.languages * 30);
      }

      return total;
    },
    updatePages(value) {
      this.pages = value;

      // console.log(arguments)
    },
    updateLanguages(value) {
      this.languages = value;
    },
    saveList(){
      this.savedBudgetList.push({
        name: this.nombre,
        client: this.client,
        cost: this.getTotal()
      });

      this.client = "";
      this.nombre = "";
      this.pages = 1;
      this.languages=1;
      this.selection=[];
      
    },
    updateList(budget){
      const newList = this.savedBudgetList.filter( b =>
        budget !== b
      )
      this.savedBudgetList = newList
    },

  },
};
</script>
<style scoped>
div {
  display: flex;
  flex-direction: column;
}

#formBox {
  /* width: 100%; */
  display: flex;
  background-color: whitesmoke;
  border-radius: 15px;
  padding: 20px 50px;
  line-height: 1cm;
  margin-top: 10px;
  justify-content: center;
}

.checkbox {
  width: 20px;
  height: 20px;
}

.backButton {
  padding: 1rem;
  font-size: 20px;
  background-color: orchid;
  border-style: solid;
  color: white;
  cursor: pointer;
  text-align: center;
  text-decoration: none;
  border-radius: 15px;
}

.saveButton {
  padding: 1rem;
  font-size: 20px;
  background-color: orchid;
  border-style: solid;
  border-color: whitesmoke;
  color: white;
  cursor: pointer;
  text-align: center;
  border-radius: 15px;
}

</style>
