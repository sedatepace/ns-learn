<template>
    <Page class="page">
      <ActionBar class="action-bar">
        <NavigationButton visibility="hidden"/>
        <GridLayout columns="50, *">
          <Label class="action-bar-title" text="FileSystem" colSpan="2"/>

          <Label class="fas" text.decode="&#xf0c9;" @tap="onDrawerButtonTap"/>
        </GridLayout>
      </ActionBar>

        <StackLayout class="page__content">
             <TextField v-model="textFieldValue" hint="Enter text..." />
          

            <Button
                text="저장하기."
                @tap="saveBtn"
                borderWidth="2"
                borderColor="#000"
                width="100%"
            />

            <Button
                text="불러오기."
                @tap="loadBtn"
                borderWidth="2"
                borderColor="#000"
                width="100%"
            />
             <Button
                text="초기화"
                @tap="resetBtn"
                borderWidth="2"
                borderColor="#000"
                width="100%"
            />
            <Label class="common" :text="JSON.stringify(current_data)" textWrap="true"/>

            <Label class="common" :text="total" textWrap="true"/>

        </StackLayout>
    </Page>
</template>

<script>
  import * as utils from "~/shared/utils";
  import { SelectedPageService } from "../shared/selected-page-service";
  
  import * as fileSystemModule from '@nativescript/core/file-system';
    

  export default {
    mounted() {
        SelectedPageService.getInstance().updateSelectedPage("Home");
        this.loadBtn();
    },
    computed: {
      message() {
        return "<!-- Page content goes here -->";
      }
    },
    methods: {
        onDrawerButtonTap() {
            utils.showDrawer();
        },

        async saveBtn(){
            try{
                this.current_data.path = this.textFieldValue;
                await this.saveFile(this.current_data);
            }catch(err){
                console.log({ERR_saveBtn:err});
            }
           
        }, //saveBtn

        async loadBtn(){
            try{
                var vm = this;
                var fileName = "data.json";
                const documents = fileSystemModule.knownFolders.documents();
                const file = documents.getFile(fileName);
                let load;
                let path = vm.textFieldValue;
                load = await file.readText()
                        .then(function(content){
                            // vm.msg = content;
                            console.log("readText ok");
                            return(content);

                        })
                        .catch((err)=>{
                            console.log(err);
                        })
                console.log({"load_chk":load});
                vm.total = load;
                load = JSON.parse(load);
                let isMapping = false
                for(i=0;i<load.length;i++){
                    if(load[i].path === path){
                        vm.current_data = JSON.stringify(load[i]);
                        isMapping = true;
                        return 0;
                    }
                }
                if(!isMapping){
                    this.current_data = {path:"", list:[]};
                }
            }catch(err){
                console.log({err});
            }
        },

        resetBtn(){
            var vm = this;
            var fileName = "data.json";
            const documents = fileSystemModule.knownFolders.documents();
            const file = documents.getFile(fileName);
            let buf = [];
            file.writeText("[]")
                .then((res)=>{
                    console.log("Complite reset");
                    vm.total=[];
                    vm.current_data ={path:"", list:[]};
                })
                .catch(((err)=>{
                        console.log('ERR_file.writeText');
                        console.log(err);
                }))
        },
        async saveFile(data){
            //데이터가 있는지 확인
            try{
                console.log("saveFile");
                console.log({data});

                var vm = this;
                var fileName = "data.json";
                const documents = fileSystemModule.knownFolders.documents();
                const file = documents.getFile(fileName);

                let load = await file.readText().then((res)=>{
                        return JSON.parse(res);
                }).catch((err)=>{
                    console.log({err});
                })
                let isEmpty =true;
                console.log({load});
                for(i=0;i<load.length;i++){
                    if(load[i].path === data.path){
                        load[i] = data;
                        isEmpty = false;
                        return 0;            
                    }
                }
                console.log({isEmpty});
                if(isEmpty){
                    for(i=0;i<5;i++){
                        data.list.push(Math.random());
                    }
                    load.push(data);
                }
                console.log(data);
                file.writeText(JSON.stringify(load)).then((res)=>{
                    console.log("Complite save");
                }).catch((err)=>{
                        console.log('ERR_file.writeText');
                        console.log(err);
                    })
            }catch(err){
                console.log(err);
            }
           
        }
    },
    data(){
        return  {
            textFieldValue:"",
            dataSample : {
                    time: 1234511,
                    memo: "etc111"
            },
            current_data: {
                path:"",
                list:[]
            },
            total:[],

        }
    }
  };
</script>

<style scoped lang="scss">
    // Start custom common variables
    @import '@nativescript/theme/scss/variables/blue';
    // End custom common variables
    .common{
        text-align: center;
        word-wrap:break-word;
    }
    // Custom styles
</style>
