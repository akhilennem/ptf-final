  <template>
  <v-app id="userattend">

    <v-navigation-drawer
      v-model="drawer"
      app
    >
      
      <v-img src="calendar.jpg">
         <div class="text-center"></div>
      </v-img>

       <v-list-item>
        <v-list-item-content>
          <v-list-item-title class="text-h6">
            Admin
          </v-list-item-title>
          <v-list-item-subtitle>
            Side Menu
          </v-list-item-subtitle>
        </v-list-item-content>
      </v-list-item>

      <v-divider></v-divider>

      <v-list
        dense
        nav
      >
        <v-list-item
          v-for="item in items"
          :key="item.title"
          :to="item.to"
          link
        >
          <v-list-item-icon>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-icon>

          <v-list-item-content>
            <v-list-item-title>{{ item.title }}</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    
    <v-app-bar app color="blue darken-1" dark>
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>
      <v-app-bar-title>Attendance</v-app-bar-title>
    </v-app-bar>

<v-img 
      src="https://theblacklily.com/wp-content/uploads/2019/10/Social-media-management-.png"
      gradient="to top right, rgba(144,146,150,.06), rgba(25,32,72,.7)"
    >
    <br>
    <!-- <h1>{{ heading }}</h1> -->
<v-container>
    <v-row >
      <v-col>
        <v-date-picker 
          v-model="date"
          @dblclick:date="dblClick"
          year-icon="mdi-calendar-blank"
          prev-icon="mdi-skip-previous"
          next-icon="mdi-skip-next"
        >
        </v-date-picker> <br>
        <v-btn color="blue" @click="generatePDF" class="white--text">Generate PDF</v-btn>
      </v-col> 
      <v-spacer></v-spacer>
        <v-col class="my-2 px-1"
        cols="12"
        sm="6">
          <div class="text-h6  mb-2">
            List of Attendance on : {{date}}
          </div>
          
           <v-data-table id="basic-table" ref="myTable"
            :headers="header"
            :items="dates"
            :items-per-page="10"
            default-footer
            class="elevation-1 mx-35"
          >   
          <li v-for="dates in items" :key="dates.id">  </li>  
    
        <td>{{dates.name}}</td>
        <td>{{dates.date}}</td>
        <td>{{dates.time}}</td>
        <td>{{dates.email}}</td>
 
          </v-data-table>
        </v-col>
      </v-row>
    </v-container>   
</v-img>
  </v-app>
</template>


<script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.6/jspdf.plugin.autotable.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.5.17-beta.0/vue.js"></script>
<script src="script.js"></script>

<script>
import axios from 'axios';
import jsPDF from 'jspdf';
import autoTable from 'jspdf-autotable';

  export default {
   data: () => ({ 
     drawer: null,
    //  heading: "PTF Attendance Sheet",
     items: [
          { title: 'Home', icon: 'mdi-home-outline' , to: '/adminhome'},
          { title: 'Profile', icon: 'mdi-account-outline' ,to: '/adminprofile' },
          { title: 'Attendance', icon: 'mdi-calendar-account-outline' },
          { title: 'Individual', icon: 'mdi-card-account-details-outline' , to: '/individualattend' },
          { title: 'About', icon: 'mdi-information-outline' , to: '/adminabout' },
        ],  
         header: [
          {
            text: 'User Name',align: 'start',sortable: false,value: 'name'},
          { text: 'Email', value: 'email' },
          { text: 'Date', value: 'date' },
          { text: 'Time', value: 'time' }
        ],
        dates:[],
        date: (new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10),
        done: [false, false, false],

     }),
   

    methods: {
      dblClick (date) {
        this.$set(this.done, 0, true)
        console.log(date); 
        axios.get('https://my--1.herokuapp.com/api/rest/get-by-date',{
        params: {
            "date": date
          } ,
      })
      .then((response)=>{
        this.dates=response.data;
        console.log(response.status);
        console.log(response.data);
      }).catch((error)=>{
        console.log(error);
      }) 
      },
      // printData()
      //   {
      //     var divToPrint= this.$refs.myTable
      //     newWin= window.open("");
      //     newWin.document.write(divToPrint.outerHTML);
      //     newWin.print();
      //     newWin.close();
      //   },

    generatePDF() {
       var doc = new jsPDF();
       var col1 = ["Name", "Email", "Date", "Time", "Signature"];
       var rows1 = [];
  /* The following array of object as response from the API req  */

       var dates = this.dates;
       dates.forEach(dates => {      
            var temp1 = [dates.name, dates.email, dates.date, dates.time, dates.sign];
            rows1.push(temp1);
        });        

        doc.autoTable(col1, rows1, { startY: 60 });
        doc.text("PTF Attendance Sheet", 14, 14);
        doc.save('Attendance.pdf');
        // doc.save(`${this.heading}.pdf`);
    }
    },    
  }
</script>

<style>

</style> 
