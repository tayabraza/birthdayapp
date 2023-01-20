<template>
  <main>
    <div class="employee-container">
        <h3 class="mb-4">Employee Details Form</h3>
        <form @submit.prevent="formSubmit">
          <input type="text" :class="{ bgNone: showModal }" placeholder="Employee Name" v-model="employeeName" required>
          <input type="text" :class="{ bgNone: showModal }" placeholder="Employee Date of Birth" id="employeeDOB" onfocus="this.type='date', this.placeholder='', this.showPicker()" v-model="employeeDOB" required>
          <input type="submit" :class="{ bgNone: showModal }" value="+" @click="$emit('getCakes', small.length, large.length)">
        </form>
        <table>
          <thead>
            <tr>
              <th>Employee Name</th>
              <th>Date of Birth</th>
              <th>Date of Cake</th>
              <th>Size of Cake</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="emp of employees" :data-title="'Birthday Holioday ' + emp.employeeHoliday">
              <td>{{emp.employeeName}}</td>
              <td>{{birthday(emp.employeeDOB)}}</td>
              <td>{{emp.employeeDOC}}</td>
              <td>{{emp.employeeSIC}}</td>
            </tr>
          </tbody>
        </table>
        <Modal v-if="showModal" />
        <div class="pie-chart">
          <Pie :data="data" :key="componentKey" />
        </div>
    </div>
  </main>
</template>

<script>
  import Modal from '@/components/Modal.vue';
  import { Chart as ChartJS, ArcElement, Tooltip, Legend } from 'chart.js';
  import { Pie } from 'vue-chartjs';
  ChartJS.register(ArcElement, Tooltip, Legend);

  export default {
    name: 'EmployeeForm',
    components: {
      Modal,
      Pie
    },
    data(){
      return {
        showModal: false,
        employees : [
        {
          employeeName: "Dave Smith",
          employeeDOB: "1982-01-14",
          employeeDOC: "January 17 2023",
          employeeSIC: "Small",
          employeeHoliday: "January 16 2023"
        }
        ],
        months : ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October",  "November",  "December"],
        employeeName: '',
        employeeDOB: '',
        uniqueEmployees : [],
        small: [],
        large: [],
        componentKey: 0,
        data: {
        labels: ['Small Cakes', 'Large Cakes'],
        datasets: [
            {
              backgroundColor: ['#00D8FF', '#DD1B16'],
              data: [1, 0],
            },
          ],
        },
      }
    },
    watch:{
      employees(newVal, oldVal){
        console.log( this.employees.sort((a, b) => a.employeeDOC - b.employeeDOC) );
      }
    },
    methods:{
      birthday(val){
        let temp = val.split('-');
        return this.months[temp[1]-1] + ' ' + temp[2]  + ' ' + temp[0];
      },
      formSubmit(){
        let unique = true;
        let pattern = /\d{2}-\d{2}$/;
        let [, bMonth, bDay] = this.employeeDOB.split('-');
        let date = new Date(new Date().getFullYear()+'-'+bMonth+'-'+bDay);
        let currentYear = new Date().getFullYear();
        let employeeDOC = '';
        let employeeHoliday = '';
        bDay = Number(bDay);
        bMonth = Number(bMonth);
        currentYear = Number(currentYear);

        switch (date.getDay()) {
          case 0:
            employeeHoliday = `${this.months[bMonth-1]} ${bDay + 1} ${currentYear}`;
            employeeDOC = `${this.months[bMonth-1]} ${bDay + 2} ${currentYear}`;
            break;
          case 5:
            employeeHoliday = `${this.months[bMonth-1]} ${bDay} ${currentYear}`;
            employeeDOC = `${this.months[bMonth-1]} ${bDay + 3} ${currentYear}`;
            break;
          case 6:
            employeeHoliday = `${this.months[bMonth-1]} ${bDay + 2} ${currentYear}`;
            employeeDOC = `${this.months[bMonth-1]} ${bDay + 3} ${currentYear}`;
            break;
          default:
            employeeHoliday = `${this.months[bMonth-1]} ${bDay} ${currentYear}`;
            employeeDOC = `${this.months[bMonth-1]} ${bDay + 1} ${currentYear}`;
        }
        
        const employeeDetails = {
          employeeName : this.employeeName.trim(),
          employeeDOB : this.employeeDOB,
          employeeDOC : employeeDOC,
          employeeSIC: 'Small',
          employeeHoliday: employeeHoliday
        }
        this.employees.forEach(v=>{
          if ( v.employeeName === employeeDetails.employeeName && v.employeeDOB === employeeDetails.employeeDOB ) {
            unique = false;
          }
          if ( v.employeeDOB.match(pattern).toString() === employeeDetails.employeeDOB.match(pattern).toString() && unique ){
               v.employeeSIC = 'Large';
               employeeDetails.employeeSIC = 'Large';
          }
        });

        if ( unique ) {
          this.employees.push(employeeDetails)
          let temp = this.employees.filter(cake => cake.employeeSIC == 'Large' );
          this.large = [...new Set(temp.map(cake => cake.employeeDOC))];
          this.small = this.employees.filter(cake => cake.employeeSIC == 'Small' );
          this.data.datasets[0].data[0] = this.small.length;
          this.data.datasets[0].data[1] = this.large.length;
          this.componentKey += 1;
        } else {
          this.showModal = true;
        }
        setTimeout(()=>{
          this.showModal = false; 
        }, 2000);
        this.employees.sort((a, b) => {
          return new Date(a.employeeDOC) - new Date(b.employeeDOC);
        });
      }
    }
  }
</script>
