<template>
    <div> 
         <!-- Begin Page Content -->
        <div class="container-fluid">
                  <span class="pageheader"><i class="fa fa-calendar"></i> Yearly - Pharmacy</span>
                    <button type="button" @click="back()" class="noprint float-right"><span class="fa fa-times"></span></button>
                    <hr/>

                      <ul class="nav nav-tabs">
            
                                <li class="nav-item" v-for="(branch,index) in branches" :key="index">
                                    <a v-if="index==0" @click="changeFilterbranch(branch.id,index)" class="active navbranch nav-link" href="javascript:void(0)">
                                             {{branch.branch}}
                                    </a>
                                    <a v-else @click="changeFilterbranch(branch.id,index)" class="navbranch nav-link" href="javascript:void(0)">
                                             {{branch.branch}}
                                    </a>
                                    
                                </li>
                             
                            </ul>   

                            
                              <div class="card">

                                <div class="card-body">
                                        <div class="row">
                                            
                                            <div class="col-md-3">
                                                <div class="input-group mb-3">
                                                    <div class="input-group-prepend">
                                                        <span class="input-group-text" id="basic-addon3">Start Year</span>
                                                    </div>
                                                    <input type="number" class="form-control" v-model="search.startyear">
                                            </div>
                                            </div>

                                            

                                            <div class="col-md-3">
                                                <div class="input-group mb-3">
                                                    <div class="input-group-prepend">
                                                        <span class="input-group-text" id="basic-addon3">End Year</span>
                                                    </div>
                                                    <input type="number" class="form-control" v-model="search.endyear"/>
                                            </div>
                                            </div>

                                             <div class="col-md-3">
                                                    <button @click="searchProceed" class="form-control btn btn-primary"><span class="fa fa-search"></span> Generate</button>
                                            </div>

                                        </div>
                                        

                                </div>
                              </div>

                                <br/>
                                  <div style="width: 100%;height:10px;background:#3A61D0;margin-bottom:50px;position:relative">
                                     <button @click="printcolumn()" class="btn btn-danger" style="position:absolute;right:0;bottom:-40px;"><span class="fa fa-file-pdf"></span> PDF</button>
                                 </div>
                                <div style="text-align:center;color:dimgray;font-size:14pt;font-weight:bold;"><span>PHARMACY SALES FROM {{search.startyear}} TO {{search.endyear}}</span></div>
                                 <column-chart :library="columnlibrary" :stacked="graphOptions.stacked" :legend="false" :download="true" :data="graphOptions.data"></column-chart>
                                        <div class="row">
                                        <div class="col-md-10 offset-md-1">
                                            <table class="table table-condensed table-striped table-bordered">
                                                <thead>
                                                    <tr style="background:#343A40;color:white;">
                                                        <th style="width:50%">Date</th>
                                                        <th>Sales</th>
                                                    </tr>
                                                </thead>

                                                <tbody>
                                                    <tr v-for="(sale,index) in sales.graph" :key="index">
                                                        <td>{{sale.yearname}}</td>
                                                        <td>{{$helper.roundToDecimal(sale.totalsales,2)}}</td>
                                                    </tr>
                                                </tbody>
                                                <tfoot>
                                                    <tr>
                                                        <th>Total</th>
                                                        <th>{{$helper.roundToDecimal(computeTotal,2)}}</th>
                                                    </tr>
                                                </tfoot>
                                            </table>
                                        </div>
                                      
                                
                              </div>

                                <div style="width: 100%;height:10px;background:#3A61D0;margin-bottom:50px;position:relative">
                                     <button @click="printpie()" class="btn btn-danger" style="position:absolute;right:0;bottom:-40px;"><span class="fa fa-file-pdf"></span> PDF</button>
                                 </div>
                                 <div style="text-align:center;color:dimgray;font-size:14pt;font-weight:bold;"><span>NO. OF SOLD ITEMS / MEDICINES FROM  {{search.startyear}} TO {{search.endyear}}</span></div>
                                      <div class="row">
                                  <div class="col-md-12">
                                      <pie-chart :library="pielibrary" :download="true" width="100%" height="500px" :data="pieOptionsMostAvail.data"></pie-chart>
                                  </div>

                                   <div class="col-md-10 offset-md-1">
                                            <table class="table table-condensed table-striped table-bordered">
                                                <thead>
                                                   <tr style="background:#343A40;color:white;">
                                                        <th style="width:50%">Medicine</th>
                                                        <th></th>
                                                    </tr>
                                                </thead>

                                                <tbody>
                                                    <tr v-for="(sale,index) in sales.serviceMostavail" :key="index">
                                                        <td>{{sale.item}}</td>
                                                        <td>{{$helper.roundToDecimal(sale.totalcount,2)}} {{sale.uom}}
                                                            <!-- <span v-if="sale.totalcount == 1">time</span><span v-else>times</span> -->
                                                        </td>
                                                    </tr>
                                                </tbody>
                                               
                                            </table>
                                        </div>
                              </div>
                              <div style="width: 100%;height:10px;background:#3A61D0;margin-bottom:50px;"/>


        </div>
  
    </div>
</template>

<script>
import { mapState } from 'vuex'
export default {

    data: function(){
        return {
                activebranchIndex: 0,
                search :{
                    startyear: "",
                    endyear: "",
                    branch: "",
                },
                 graphOptions: {
                    stacked: false,
                    colors: ['#b00', '#666'],
                    data: [],
                },
                pieOptionsMostAvail: {
                    data: [],
                },
                columnlibrary: {
                    animation: {
                        onComplete: this.doneChart
                    }
                },
                pielibrary: {
                    animation: {
                        onComplete: this.donePie
                    }
                },
                columnimg: null,
                pieimg: null,

        }
    },
    methods: {
            doneChart: function(){
            let charts = document.getElementsByClassName('chartjs-render-monitor')
            this.columnimg = charts[0].toDataURL()
        },
        donePie: function(){
            let charts = document.getElementsByClassName('chartjs-render-monitor')
            this.pieimg = charts[1].toDataURL()
        },
         printpie: function(){
               let data = this.sales.serviceMostavail.map((sale)=>{
                return [sale.item,`${this.$helper.roundToDecimal(sale.totalcount,2)} ${sale.uom}`]
            })

            data.unshift(['Medicine',''])

             var docDefinition = {  
                 
                    // header: {text: 'Simple Text', margin: 10 , alignment: 'center'},  
                watermark: { text: 'AnzaYap Dental Clinic', color: 'blue', opacity: 0.1, bold: true, italics: false },
                footer: function(currentPage, pageCount) { 
                  return { text: currentPage.toString() + ' of ' + pageCount , alignment: 'center'}; 
                  },
                pageOrientation: 'portrait',
               content: [
                 {
                   text: `${this.branches[this.activebranchIndex].branch} No. Of Sold Items / Medicine From ${this.search.startyear} To ${this.search.endyear}`, alignment: 'center', margin: [0,0,0,20]
                 },
                 {
                    image: this.pieimg,
                    height:400,
                    width: 1200,
                    alignment: 'center',
                    margin: [0,0,0,20]
                 },
                {
                 table: {
                   widths: ['*','*'],
                   body: data,
                   pageBreak: 'after'
                 }
               },
           
              ]
              
            }
            this.$pdfMake.createPdf(docDefinition).open();
         },
         printcolumn: function(){

            let data = this.sales.graph.map((sale)=>{
                return [sale.yearname,this.$helper.roundToDecimal(sale.totalsales,2)]
            })

            data.unshift(['DATE','EARNINGS'])

            data.push(['Total',this.$helper.roundToDecimal(this.computeTotal,2)])
             var docDefinition = {  
                 
                    // header: {text: 'Simple Text', margin: 10 , alignment: 'center'},  
                watermark: { text: 'AnzaYap Dental Clinic', color: 'blue', opacity: 0.1, bold: true, italics: false },
                footer: function(currentPage, pageCount) { 
                  return { text: currentPage.toString() + ' of ' + pageCount , alignment: 'center'}; 
                  },
                pageOrientation: 'landscape',
               content: [
                 {
                   text: `${this.branches[this.activebranchIndex].branch} Pharmacy Sales From ${this.search.startyear} To ${this.search.endyear}`, alignment: 'center', margin: [0,0,0,20]
                 },
                 {
                     image: this.columnimg,
                     height:300,
                     width:750,
                 },
                {
                 table: {
                   widths: ['*','*'],
                   body: data,
                   pageBreak: 'after'
                 }
               },
           
              ]
              
            }
            this.$pdfMake.createPdf(docDefinition).open();
        },
        back: function(){
            this.$router.go(-1)
        },
        changeFilterbranch: function(branch,index){
                const navbranch = document.getElementsByClassName('navbranch')
                for(let x = 0 ; x < navbranch.length ; x++){
                    navbranch[x].classList.remove('active')
                }
                navbranch[index].classList.add('active')
                this.activebranchIndex = index
                this.search.branch = this.branches[this.activebranchIndex].id
                this.searchProceed()

         },

         searchProceed: function(){
              this.$store.dispatch("report/pharmacy_yearly",this.search)
                .then(()=>{
                     let data = []
                    this.sales.graph.forEach((sale)=>{
                        data.push([sale.yearname, sale.totalsales])
                    })
                    this.graphOptions.data = data
                    data = []
                     this.sales.serviceMostavail.forEach((sale)=>{
                        data.push([sale.item, sale.totalcount])
                    })
                    this.pieOptionsMostAvail.data = data
                })
                .catch(err=>console.log(err))
         }
    },
    computed: {
        ...mapState({
            branches: state=> state.branch.branches,
            sales: state=> state.report.sales,
        }),
        computeTotal: {
            get: function(){
                let total = 0
             this.sales.graph.forEach((sale)=>{
                 total = total + parseFloat(sale.totalsales)
             })
             return total
            }
        }
    },
    mounted(){
        this.$store.dispatch("activenav","reportnav")
          this.$store.dispatch("branch/getList")
            .then(()=>{
                    const datenow = new Date()
                    this.search.startyear = this.search.endyear = datenow.getFullYear()
                    this.search.branch = this.branches[this.activebranchIndex].id
                    this.searchProceed()
                })
            .catch(err=>console.log(err))
    }

}
</script>