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
              id="dataTable"
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
                                  v-mask="maskTrainNumber"
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
                                  v-mask="maskInvoiceId"
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
                                  hint="ЭО######"
                                  v-mask="maskInvoiceNumber"
                                  required

                                > 
                                </v-text-field>
                              </v-col>
                               <v-col cols="12" sm="6" md="6">
                                <v-text-field color="red" disabled v-model="editedItem.stateId" label="state id"></v-text-field>
                              </v-col>
                               <v-col cols="12" sm="6" md="6">
                                <v-menu
                                  :close-on-content-click="false"
                                  v-model="menu"
                                  :nudge-right="40"
                                  transition="scale-transition"
                                  offset-y
                                  full-width
                                  min-width="290px"
                                >
                                  <template v-slot:activator="{ on }">
                                    <v-text-field 
                                      color="black"
                                      v-on="on"
                                      v-model="editedItem.lastOperDt" 
                                      label="Дата-время операции"
                                      hint="ДД.ММ.ГГГГ, ЧЧ:ММ"
                                      v-mask="maskLastOperDt"
                                      readonly
                                    >
                                    </v-text-field>
                                  </template>  
                                  <v-date-picker
                                    ref="picker"
                                    v-model="myNewDate"
                                    no-title 
                                    scrollable
                                    :max="maxDate()"
                                    min="01-01-1950"

                                  >
                                    <v-spacer></v-spacer>
                                    <v-btn text color="red" @click="menu = false">Cancel</v-btn>
                                    <v-btn text color="red" @click="formatDate(myNewDate)">OK</v-btn>
                                  </v-date-picker>
                                </v-menu>  
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
                    day: 'numeric',
                    month: 'numeric',
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
  import userDataJson from '../public/table-data.json'

  export default {
    data: () => ({
      userDataJson: userDataJson,
      info: null,
      dialog: false,
      valid: false,
      menu: false,
      myNewDate: '',
      maskTrainNumber: '####',
      maskInvoiceNumber: 'ЭО######',
      maskInvoiceId: '#########',
      maskLastOperDt: '##/##/####, ##:##',
      trainNumberRules: [
        v => !!v || 'Требуется номер поезда',
        v => (v && v.length >= 4 && v.length <= 4) ||  'Номер должен быть равен 4 символам'
      ],
      invoiceIdRules: [
        v => !!v || 'Требуется ИД накладной',
        v => (v && v.length >= 9 && v.length <= 9) ||  'ИД накладной долджно быть 9 символов'
      ],
      invoiceNumberRules: [
        v => !!v || 'Требуется номер накладной',
        v => (v && v.length >= 8) ||  'В номере накладной должно быть 8 символов'
      ],

      headers: [
        { text: '№ п/п', align: 'end', value: 'ordNumber'},
        { text: 'Номер вагона', align: 'end', value: 'carNumber' },
        { text: 'Индекс поезда', align: 'end', value: 'trainIndex' },
        { text: 'Номер поезда', align: 'end', value: 'trainNumber' },
        { text: 'Статус', align: 'end', value: 'carStatus' },
        { text: 'Дата-время операции', align: 'end', value: 'lastOperDt' },
        { text: '№ Накладной', align: 'end', value: 'invoiceNumber' },
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

    filters: {
      filterFormatDataTime(value) {
        
        if(value) {

          const [month, day, year] = value.split('.')
          value = `${day}.${month}.${year}`
        } else {
          value = '-'
        }
        return value;
      }
    },

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
      formatDate (date) {
        if (!date) return null
        this.menu = false
        const [year, month, day] = date.split('-')
        return this.editedItem.lastOperDt = `${day}.${month}.${year}`
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
      maxDate() {
        const date = new Date();
        let newDate = date.toLocaleString("ru-RU", 
                  {
                    year: 'numeric',
                    month: 'numeric',
                    day: 'numeric',
                  }
                );

        const [month, day, year] = newDate.split('.')
        return newDate = `${year}-${day}-${month}`
      },
      initialize () {
        this.desserts = this.userDataJson
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
  
  #dataTable th {
    text-align: center !important;
  }
   #dataTable td {
    text-align: center !important;
  }

</style>