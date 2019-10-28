<template>
  <div class="md-layout">
    <div class="md-layout-item">
      <signup-card>
        <h2 class="title text-center" slot="title">Register</h2>
        
        <div
          class="md-layout-item mr-auto"
          slot="content-right"
        >
            <p v-if="errors.length">
              <b>Lỗi</b>
              <ul>
                <li v-for="(error , index) in errors" v-bind:key = "index">{{ error }}</li>
              </ul>
            </p>
          <form >
  
            <div class="form-group">
              <label for="username">UserName:</label>
              <input type="text" class="form-control" id="username" placeholder="Enter username" name="username" v-model="username">
            </div>
            <div class="form-group">
              <label for="email">Email:</label>
              <div :class="['input-group', isEmailValid()]">
                <input type="email" class="form-control" id="email" placeholder="Enter email" name="email" v-model="email">
              </div>
            </div>
            
            <div class="form-group">
              <label for="pwd">Password:</label>
              <input  v-validate="'required'" type="password" class="form-control" id="pwd" placeholder="Enter password" name="password" v-model="password">
            </div>
            
            <div class="form-group">
              <label for="pwd">Password Confirm:</label>
              <input v-validate="'required|confirmed:password'"  type="password" class="form-control" id="pwd" placeholder="Enter password confirm" name="passwordconfirm" v-model="passwordconfirm">
            </div>
            
             <p v-if="errorpassconfirm.length">
              <b>Lỗi</b>
              <ul>
                <li v-for="(error , index) in errorpassconfirm" v-bind:key = "index">{{ error }}</li>
              </ul>
            </p>
          </form>
          <button type="submit" class="btn btn-primary" v-on:click="checkForm " >Đăng ký</button>

          <b-modal ref="my-modal" hide-footer title="Verification">
            <div class="d-block text-center">
              <h3>Hello!</h3>
              <p>Đây có phải là email <b><i> {{email}} </i></b> của bạn. 
              Nếu đúng bấm Continue để tiếp tục.
              Nếu không bấm Close để nhập  lại.</p>
            </div>
            <b-button class="mt-2" variant="outline-warning" block v-on:click="toggleModal">Continue</b-button>
            <b-button class="mt-3" variant="outline-danger" block v-on:click="hideModal">Close</b-button>
          </b-modal>
        </div>
      </signup-card>
    </div>
  </div>
</template>
<script>
import axios from 'axios';
import { SignupCard } from "@/components";

export default {
  components: {
    SignupCard
  },
  data() {
    return {
      errors:[],
      errorpassconfirm : [],
      username: '',
      boolean: false, 
      email: '',
      reg: /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,24}))$/,
      password: '',
      passwordconfirm: '' 
    }
  },
  methods: {
        checkForm : function() {
          
          if(this.username && this.email && this.password && this.passwordconfirm){
            this.errors = [];
            this.confirm();
            return true;
          } else {
            this.errors = [];
            if(this.username=='') {
              this.errors.push("Nhập UserName");
            };
            if(this.email=='') {
              this.errors.push("Nhập email");
            };
            if(this.password=='') {
              this.errors.push("Nhập password");
            };
            if(this.passwordconfirm=='') {
             this.errors.push("Nhập passwordconfirm");
            }
          }
        },

        isEmailValid: function() {
          return (this.email == "")? "" : (this.reg.test(this.email)) ? 'has-success' : 'has-error';
        },

        confirm : function () {
          if ( this.password === this.passwordconfirm) {
            this.errorpassconfirm = [];
            this.$refs['my-modal'].show();
            return true;
          } else {
            this.errorpassconfirm = [];
            this.errorpassconfirm.push("Nhập lại Password")
            return false;
          }
        },

        hideModal() {
          this.$refs['my-modal'].hide()
        },

        toggleModal() {
          // We pass the ID of the button that we want to return focus to
          // when the modal has hidden
          this.created();
          this.$swal('Sucess');
          this.$refs['my-modal'].toggle('#toggle-btn')
        },
        created() {
          axios.get(`http://localhost:8085/api/auth/signup`)
          .then(response => {
            this.email = response.data
          })
          .catch(e => {
            this.errors.push(e)
          })
        }
      }
};
</script>
<style>

</style>
