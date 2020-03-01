<template>
   <v-app id="inspire">
    <v-content>
      <v-container
        class="fill-height app-container"
        fluid
      >
        <v-row
          align="center"
          justify="center"
        >
          <v-col
            cols="12"
            sm="10"
          >
            <v-data-table
              :headers="headers"
              :items="desserts"
              sort-by="calories"
              class="elevation-1"
              mobile-breakpoint="767"
              @click:row ="editItem"
            >
              <template v-slot:top>
                  <v-dialog v-model="dialog" max-width="500px"> 
                    <v-card>
                      <v-card-title>
                        <span class="headline">Внести изменения</span>
                      </v-card-title>

                      <v-card-text>
                        <v-form v-model="valid" ref="form">   
                          <v-container> 
                            <v-row>
                              <v-col cols="12" sm="6" md="6">
                                <v-text-field color="red" disabled v-model="editedItem.ordNumber" label="№ п/п"></v-text-field>
                              </v-col>
                              <v-col cols="12" sm="6" md="6">
                                <v-text-field color="red" disabled v-model="editedItem.carNumber" label="Номер вагона"></v-text-field>
                              </v-col>
                              <v-col cols="12" sm="6" md="6">
                                <v-text-field color="red" disabled v-model="editedItem.trainIndex" label="Индекс поезда"></v-text-field>
                              </v-col>
                              <v-col cols="12" sm="6" md="6">
                                <v-text-field 
                                  color="black" 
                                  v-model="editedItem.trainNumber" 
                                  label="Номер поезда"
                                  :rules="trainNumberRules"
                                  :counter="4"
                                  type="number"
                                  required
                                > 
                                </v-text-field>
                              </v-col>
                              <v-col cols="12" sm="6" md="6">
                                <v-text-field color="red" disabled v-model="editedItem.carStatus" label="Статус"></v-text-field>
                              </v-col>
                              <v-col cols="12" sm="6" md="6">
                                <v-text-field 
                                  color="black"
                                  v-model="editedItem.invoiceId"
                                  label="ИД Накладной"
                                  :rules="invoiceIdRules"
                                  :counter="9"
                                  type="number"
                                  required
                                >
                                </v-text-field>
                              </v-col>
                               <v-col cols="12" sm="6" md="6">
                                <v-text-field 
                                  color="black" 
                                  v-model="editedItem.invoiceNumber" 
                                  label="№ Накладной"
                                  :rules="invoiceNumberRules"
                                  :counter="8"
                                > 
                                </v-text-field>
                              </v-col>
                               <v-col cols="12" sm="6" md="6">
                                <v-text-field color="red" disabled v-model="editedItem.stateId" label="state id"></v-text-field>
                              </v-col>
                               <v-col cols="12" sm="6" md="6">
                                <v-text-field 
                                  color="black" 
                                  v-model="editedItem.lastOperDt" 
                                  label="Дата-время операции"
                                  :rules="lastOperDtRules"
                                  hint="ДД/ММ/ГГГГ"
                                  
                                  required
                                >
                                </v-text-field>
                              </v-col>
                            </v-row>
                          </v-container>
                        </v-form>  
                      </v-card-text>

                      <v-card-actions>
                        <v-spacer></v-spacer>
                        <v-btn 
                          color="red darken-1" 
                          text @click="save"
                          :disabled="!valid"
                        >OK</v-btn>
                        <v-btn color="red darken-1" text @click="close">Отмена</v-btn>
                      </v-card-actions>
                    </v-card>
                  </v-dialog>
              </template>
              <template v-slot:item.lastOperDt="{ item }">
                <span v-if="item.lastOperDt">{{new Date(item.lastOperDt).toLocaleString("ru-RU", 
                  {
                    year: 'numeric',
                    month: 'numeric',
                    day: 'numeric',
                    hour: 'numeric',
                    minute: 'numeric'
                  }
                )}}</span>
                <span v-else>-</span>
              </template>  
              <template v-slot:no-data>
                <v-btn color="primary" @click="initialize">Reset</v-btn>
              </template>
            </v-data-table>
          </v-col>
        </v-row>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
    export default {
    data: () => ({
      dialog: false,
      valid: false,
      trainNumberRules: [
        v => !!v || 'Требуется номер поезда',
        v => (v.length >= 4 && v.length <= 4) ||  'Номер должен быть равен 4 символам'
      ],
      invoiceIdRules: [
        v => !!v || 'Требуется ИД накладной',
        v => (v.length >= 9 && v.length <= 9) ||  'ИД накладной долджно быть 9 символов'
      ],
      invoiceNumberRules: [
        v => !!v || 'Требуется номер накладной',
        v => v.length >= 8 ||  'Name must be less than 8 characters'
      ],
      lastOperDtRules: [
        v => !!v || 'Требуется дата',
        v => v.length >= 8 ||  'Name must be less than 8 characters'
      ],
      headers: [
        { text: '№ п/п', align: 'end', value: 'ordNumber'},
        { text: 'Номер вагона', value: 'carNumber' },
        { text: 'Индекс поезда', value: 'trainIndex' },
        { text: 'Номер поезда', align: 'center', value: 'trainNumber' },
        { text: 'Статус', align: 'center', value: 'carStatus' },
        { text: 'Дата-время операции', align: 'center', value: 'lastOperDt' },
        { text: '№ Накладной', value: 'invoiceNumber' },
        { text: 'ИД Накладной', align: 'end', value: 'invoiceId' },
        { text: 'state id', align: 'end', value: 'stateId' },
      ],
      desserts: [],
      editedIndex: -1,
      editedItem: {
        ordNumber: 0,
        carNumber: 0,
        trainIndex: 0,
        trainNumber: 0,
        carStatus: '',
        invoiceId: 0,
        invoiceNumber: '',
        stateId: 0,
        lastOperDt: 0
      },
      defaultItem: {
        ordNumber: 0,
        carNumber: 0,
        trainIndex: 0,
        trainNumber: 0,
        carStatus: '',
        invoiceId: 0,
        invoiceNumber: '',
        stateId: 0,
        lastOperDt: 0
      },
    }),

    watch: {
      dialog (val) {
        val || this.close()
      },
    },

    created () {
      this.initialize()
    },

    methods: {
      onSubmit () {
        if (this.$refs.form.validate()) {
          const user = {
            trainNumber: this.editedItem.trainNumber
          }
          window.console.log(user) 
        }
      },
      formatDataTime(value) {
        if(value) {
          value = new Date(value).toLocaleString("ru-RU", 
            {
              year: 'numeric',
              month: 'numeric',
              day: 'numeric',
              hour: 'numeric',
              minute: 'numeric'
            }
          )

        } else {
          value = '-'
        }
        return value;
      },
      initialize () {
        this.desserts = [
          {
              "ordNumber": 1,
              "carNumber": "94003894",
              "trainIndex": "035004766035601",
              "trainNumber": "9999",
              "carStatus": "составлен в поезд",
              "invoiceId": "923210370",
              "invoiceNumber": "ЭО560089",
              "stateId": 34,
              "lastOperDt": "2019-08-11T20:51:00.000Z"
          },
          {
              "ordNumber": 2,
              "carNumber": "94139292",
              "trainIndex": "035004766035601",
              "trainNumber": "9999",
              "carStatus": "составлен в поезд",
              "invoiceId": "923210370",
              "invoiceNumber": "ЭО560089",
              "stateId": 34,
              "lastOperDt": "2019-08-12T20:55:00.000Z"
          },
          {
              "ordNumber": 3,
              "carNumber": "94189768",
              "trainIndex": null,
              "trainNumber": null,
              "carStatus": "",
              "invoiceId": "924094926",
              "invoiceNumber": "ЭО730743",
              "stateId": 3,
              "lastOperDt": null
          }
        ]
      },

      editItem (item) {
        this.editedIndex = this.desserts.indexOf(item)
        this.editedItem = Object.assign({}, item)
        let formatData =  this.formatDataTime(item.lastOperDt)
        this.editedItem.lastOperDt = formatData;
        this.dialog = true
      },

      close () {
        this.dialog = false
        setTimeout(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        }, 300)
      },

      save () {
        this.onSubmit(); 
        Object.assign(this.desserts[this.editedIndex], this.editedItem)
        this.close()
      },
    },
  }
</script>

<style>
  .app-container {
    background: #f6f6f6; 
  }
  /* 
  .table hr {
    text-align: start !important;
  }
  .table td:first-child  {
    padding-right: 2.8em !important;
  }
  
  .table td:last-child  {
    padding-right: 2.8em !important;
  } */
</style>


<!-- <template v-slot:item.lastOperDt="{ item }">
                <div v-if="item.lastOperDt">{{new Date(item.lastOperDt).toLocaleString("ru-RU", 
                  {
                      year: 'numeric',
                      month: 'numeric',
                      day: 'numeric',
                      hour: 'numeric',
                      minute: 'numeric'
                  }
                )}}</div>
                <div v-else><p class="text-sm-center">-</p></div>
             </template> -->