<template>
  <div>
    <header style="position: fixed; width: 100%;">
      <strong style="font-size: 32px;">RECETAS DE NEVERA</strong>
    </header>
    <div id="main">
      <nav style="height: 100vh; padding-top: 11vh">
        <div class="top container">
          <form @submit.prevent="addIngredient">
            <div class="form-group">
              <h5 for="ingredient">Ingredientes:</h5>
              <v-autocomplete label="Escoja sus ingredientes" :items="allIngredients"
                v-model="newIngredient"></v-autocomplete>
            </div>
            <button type="submit" class="btn btn-primary">Add</button>
          </form>
          <br>
          <div class="card">
            <div class="card-header" @click="toggleCollapseAl">
              <h5 class="mb-0">
                Alergenos
              </h5>
            </div>
            <div :class="['collapse', { show: isCollapsedAl }]">
              <div class="card-body">
                <v-combobox v-model="chips" :items="availableAlergenos" chips clearable label="Alérgenos" multiple
                  prepend-icon="mdi-filter-variant" variant="solo" @change="addAlergenos">
                  <template v-slot:selection="{ attrs, item, select, selected }">
                    <v-chip v-bind="attrs" :model-value="selected" closable @click="select" @click:close="remove(item)">
                      <strong>{{ item }}</strong>&nbsp;
                      <span>{{ alergeno }}</span>
                    </v-chip>
                  </template>
                </v-combobox>
              </div>
            </div>
          </div>
          <br>
          <div class="card">
            <div class="card-header" @click="toggleCollapseDi">
              <h5 class="mb-0">
                Dieta
              </h5>
              <div :class="['collapse', { show: isCollapsedDi }]">
                <div class="card-body">
                  <button v-for="dieta in availableDietas" :key="dieta" type="button" @click="addDietas(dieta)"
                    :class="{ clicked: contains(dietas, dieta) }">
                    {{ dieta }}
                  </button>
                </div>
              </div>
            </div>
          </div>
          <br>
          <ul class="list-unstyled components">
            <br>
            <h5>Tiempo</h5>
            <div class="Slidecontainer">
              <input type="range" value="100" min="15" max="400" oninput="this.nextElementSibling.value = this.value"
                class="slider" id="time_slider">
              <output>100</output>
            </div>
            <br>
            <h5>Tipo de Plato:</h5>
            <li>
              <select class="selectpicker" id="ute_SelectPicker">
                <option value="0" selected disabled hidden>¿Que tipo de plato quieres?</option>
                <option value="Desayuno">Desayuno</option>
                <option value="Entrante">Entrante</option>
                <option value="Principal">Principal</option>
                <option value="Postre">Postre</option>
                <option value="0">No seleccionar</option>
              </select>
            </li>
          </ul>
        </div>
        <div class="bot">
          <button class="btn btn-success btn-lg" @click="getReceipts" :disabled="ingredients.length < 1"> Buscar Recetas
          </button>
        </div>
      </nav>
      <div class="container">

          <article class="col-sm-12 container-fluid" style="height: 100vh; padding-top: 11vh">
            <div class="row">
              <div id="content" class="col-sm-12">
                <h2> RECETAS</h2>
                <p id="receptes"></p>
              </div>
            </div>
                <div class="row">
                  <div class="col-lg-3 col-md-6  col-sm-12 d-flex align-items-stretch"
                    v-for="(reciept, index) in availableReciepts" :key="index">
                    <v-card class="card mb-4 shadow-sm">
                      <div class="card-body">
                        <v-image :src="reciept.imagen" alt="reciept.nombre" width="100%"/>
                        <v-card-title>{{ reciept.nombre }}</v-card-title>
                        <v-card-text>
                          <h5>Ingredientes:</h5>
                          <ul>
                            <li v-for="(ingrediente, index) in reciept.ingredientes" :key="index">{{ ingrediente }}</li>
                          </ul>
                          <a href="#" class="btn btn-primary">Ver receta</a>
                        </v-card-text>
                      </div>
                    </v-card>
                  </div>
                </div>
            
          </article>
      </div>
      <div class="leftCol container" style="height: 100vh; width: 350px; padding-top: 11vh">
        <div class="col-sm-12">
          <ul>
            <li v-for="(alergeno, index) in alergenos" :key="index">{{ alergeno }}</li>
          </ul>
          <ul>
            <li v-for="(dieta, index) in dietas" :key="index">{{ dieta }}</li>
          </ul>
          <ul>
            <li v-for="(ingredientes, index) in ingredients" :key="index">{{ ingredientes }}</li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>
<script>

export default {
  name: 'StartingPage',
  data() {
    return {
      newIngredient: '',
      ingredients: [],
      availableReciepts: [],
      receipts: [
        {
          'nombre': 'Macarrones con tomate',
          'ingredientes': ['macarrones', 'tomate', 'queso'],
          'alergenos': ['Gluten', 'Huevos', 'Laácteos'],
          'Dietas': ['Al-halal'],
          "imagen": "https://recetaslife.com/wp-content/uploads/2015/10/receta-macarrones-tomate.jpg"
        },
        {
          "nombre": "Pollo al horno con patatas",
          "ingredientes": ["pollo", "patatas", "cebolla", "ajo", "aceite de oliva", "sal", "pimienta"],
          "alergenos": ["Ninguno"],
          "dietas": ["Sin gluten"],
          "imagen": "https://recetaslife.com/wp-content/uploads/2015/10/receta-macarrones-tomate.jpg"
        }

      ],
      availableDietas: ['vegano', 'Sin gluten', 'Kosher', 'Al-halal', 'Sin Lactosa'],
      availableAlergenos: ['Gluten', 'Crustáceos', 'Huevos', 'Pescado',
        'Cacahuete', 'Soja', 'Lácteos', 'Frutos con cáscaras', 'Moluscos'],
      alergenos: [],
      allIngredients: ['pollo', 'patatas', 'cebolla', 'ajo', 'aceite de oliva', 'sal', 'pimienta', 'lechuga', 'pan', 'queso parmesano', 'mostaza', 'vinagre', 'salsa Worcestershire', 'limón', 'harina', 'mantequilla', 'azúcar', 'huevo', 'levadura', 'canela', 'alga nori', 'salmón', 'aguacate', 'pepino', 'salsa de soja', 'wasabi', 'carne de vacuno', 'queso cheddar', 'pan de hamburguesa', 'tomate', 'ketchup', 'mayonesa', 'arroz', 'huevos', 'plátanos', 'tomate frito', 'judía verde', 'garrofón', 'pimiento rojo', 'pimiento verde', 'azafrán', 'caldo de pollo', 'pasta de lasaña', 'carne picada', 'nuez moscada'],
      dietas: [],
      isCollapsedAl: false,
      isCollapsedDi: false
    }
  },
  methods: {
    addIngredient() {
      if (this.newIngredient.trim() !== '') {
        if (this.ingredients.includes(this.newIngredient.toLowerCase())) {
          const index = this.ingredients.indexOf(this.newIngredient.toLowerCase())
          if (index > -1) {
            this.ingredients.splice(index, 1)
          }
        } else {
          this.ingredients.push(this.newIngredient.toLowerCase())
        }
        this.newIngredient = ''
      }
    },
    getReceipts() {
      this.availableReciepts = []
      this.receipts.forEach((receipt) => {
        if (this.ingredients.every((ingredient) => receipt.ingredientes.map(i => i.toLowerCase()).includes(ingredient.toLowerCase()))) {
          this.availableReciepts.push(receipt)
        }
      })
    },
    toggleCollapseAl() {
      this.isCollapsedAl = !this.isCollapsedAl
    },
    toggleCollapseDi() {
      this.isCollapsedDi = !this.isCollapsedDi
    },
    addAlergenos(alergeno) {
      this.alergenos = alergeno;
    },
    contains: function (arr, item) {
      return arr.indexOf(item) !== -1
    },
    addDietas(dieta) {
      if (this.dietas.includes(dieta)) {
        const index = this.dietas.indexOf(dieta)
        if (index > -1) {
          this.dietas.splice(index, 1)
        }
      } else {
        this.dietas.push(dieta)
      }
    }
  }, created() {
    this.availableReciepts = this.receipts;
  }
}
</script>

<style>
body {
  display: flex;
  flex-direction: column;
  background-color: #8a8a8a;
  margin: 0;
  background-size: 1400px;
  width: 100%;
  height: 100%;
}

#main {
  display: flex;
  flex-grow: 1;
  min-height: calc(100vh - 20vh);
}

#main>nav {
  display: flex;
  flex: 0 0 10vh;
  width: auto;
  background: #6F6F6F;
  flex-direction: column;
}

#main>.leftCol {
  margin-right: 0px;
  margin-left: auto;
  width: auto;
  background: #6f6f6f;
}

#man>article {
  order: 1;
}

#main>nav {
  order: -1;
}

nav>.top {
  flex-grow: 1;
  background: #6F6F6F;
  overflow: auto;
}

nav>.bot {
  padding: 8px;
  text-align: center;
  background: #404040;
  border-radius: 5px;
  width: 100%;
}

header,
footer,
article,
nav,
aside {
  padding: 1em;
}

header {
  text-align: center;
  color: white;
  background: #404040;
  height: 10vh;
}

footer {
  background: #404040;
  height: 10vh;
  align-content: end;
  vertical-align: middle;
  text-align: right;
  color: white;
}

table,
th,
td {
  border: 1px solid;
  border-collapse: collapse;
  background-color: white;
}

.clicked {
  background-color: blueviolet;
  color: white;
}

v-card {
  margin: 10px;
  background-color: #6f6f6f;
  color: white;
}

v-image {
  width: 100%;
  height: 100%;
}


</style>
