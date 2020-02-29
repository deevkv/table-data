<template>
   <v-app id="inspire">
    <v-content>
      <v-container
        class="fill-height container"
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
              item-key="ordNumber"
              class="pa-3 elevation-1" 
            > 
             <!-- <template v-slot:item="props">
                <tr>
                 <td class="text-left pa-3">{{ props.item.ordNumber }}</td>
                 <td class="text-left">{{ props.item.carNumber }}</td>
                 <td class="text-xs-right">{{ props.item.trainIndex }}</td>
                 <td class="text-center">{{ props.item.trainNumber }}</td>
                 <td class="text-xs-right">{{ props.item.carStatus }}</td>
                 <td class="text-xs-right" >{{ props.item.lastOperDt }}</td>
                 <td class="text-xs-right">{{ props.item.invoiceNumber }}</td>
                 <td class="text-xs-right">{{ props.item.invoiceId }}</td>
                 <td>{{ props.item.stateId }}</td>
               </tr>  
             </template> -->
              <template v-slot:item.lastOperDt="{ item }">
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
    data () {
      return {
        headers: [
          { text: '№ п/п', value: 'ordNumber'},
          { text: 'Номер вагона', value: 'carNumber' },
          { text: 'Индекс поезда', value: 'trainIndex' },
          { text: 'Номер поезда', align: 'center', value: 'trainNumber' },
          { text: 'Статус', align: 'center', value: 'carStatus' },
          { text: 'Дата-время операции', align: 'center', value: 'lastOperDt' },
          { text: '№ Накладной', value: 'invoiceNumber' },
          { text: 'ИД Накладной', value: 'invoiceId' },
          { text: 'state id', value: 'stateId' }

        ],
        desserts: [
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
          },
          {
              "ordNumber": 4,
              "carNumber": "94950078",
              "trainIndex": null,
              "trainNumber": null,
              "carStatus": "",
              "invoiceId": "924094926",
              "invoiceNumber": "ЭО730743",
              "stateId": 3,
              "lastOperDt": null
          },
          {
              "ordNumber": 5,
              "carNumber": "54964036",
              "trainIndex": null,
              "trainNumber": null,
              "carStatus": "",
              "invoiceId": "924094926",
              "invoiceNumber": "ЭО730743",
              "stateId": 3,
              "lastOperDt": null
          },
          {
              "ordNumber": 6,
              "carNumber": "94949708",
              "trainIndex": null,
              "trainNumber": null,
              "carStatus": "",
              "invoiceId": "924094926",
              "invoiceNumber": "ЭО730743",
              "stateId": 3,
              "lastOperDt": null
          },
          {
              "ordNumber": 7,
              "carNumber": "94395381",
              "trainIndex": null,
              "trainNumber": null,
              "carStatus": "",
              "invoiceId": "924094926",
              "invoiceNumber": "ЭО730743",
              "stateId": 3,
              "lastOperDt": null
          },
          {
              "ordNumber": 8,
              "carNumber": "94420957",
              "trainIndex": null,
              "trainNumber": null,
              "carStatus": "осмотрен в техническом отношении",
              "invoiceId": "924094926",
              "invoiceNumber": "ЭО730743",
              "stateId": 35,
              "lastOperDt": "2019-08-14T10:04:00.000Z"
          },
          {
              "ordNumber": 9,
              "carNumber": "94693793",
              "trainIndex": null,
              "trainNumber": null,
              "carStatus": "осмотрен в техническом отношении",
              "invoiceId": "924094926",
              "invoiceNumber": "ЭО730743",
              "stateId": 35,
              "lastOperDt": "2019-08-14T10:04:00.000Z"
          },
          {
              "ordNumber": 10,
              "carNumber": "59910554",
              "trainIndex": null,
              "trainNumber": null,
              "carStatus": "осмотрен в техническом отношении",
              "invoiceId": "924094926",
              "invoiceNumber": "ЭО730743",
              "stateId": 35,
              "lastOperDt": "2019-08-14T14:22:00.000Z"
          },
          {
              "ordNumber": 11,
              "carNumber": "94949773",
              "trainIndex": null,
              "trainNumber": null,
              "carStatus": "осмотрен в техническом отношении",
              "invoiceId": "924094926",
              "invoiceNumber": "ЭО730743",
              "stateId": 35,
              "lastOperDt": "2019-07-28T00:30:00.000Z"
          },
          {
              "ordNumber": 12,
              "carNumber": "94968922",
              "trainIndex": null,
              "trainNumber": null,
              "carStatus": "осмотрен в техническом отношении",
              "invoiceId": "924094926",
              "invoiceNumber": "ЭО730743",
              "stateId": 35,
              "lastOperDt": "2019-08-14T10:04:00.000Z"
          }
        ],
        methods: {
          
        },
      }
    }
  }
</script>

<style>
  .container {
    background: #f6f6f6; 
  }
</style>
