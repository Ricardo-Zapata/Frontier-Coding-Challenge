<template>
  <div id="app">
    <main class="content grid" id="home-content">
      <div class="content-title-container">
        <h2>Accounts</h2>
      </div>
      <section class="grid" id="account-grid">
        <section class="account-column grid" id="active-account-column">
          <div class="account-container-title" id="active-account-container-title">
            <h3>Active</h3>
          </div> <!-- lists are repeated. They could be separated into their own component -->
          <div class="account-container active-account" v-for="account in activeAccounts" v-bind:key="account.Id">
            <ul class="account-data-list">
              <li><label>Name:</label>{{account.LastName}}, {{account.FirstName}}</li>
              <li><label>Email:</label>{{account.Email}}</li>
              <li><label>Phone Number:</label>{{account.PhoneNumber.replace(/(\d{3})(\d{3})(\d{4})/, '($1)-$2-$3')}}</li>  <!-- formating for phone number.
              Phone number formatting learned from https://gist.github.com/kkiernan/7475b615485344f2fdf3 -->
              <li><label>Amount Due:</label>${{account.AmountDue}}</li>
              <li><label>Due Date:</label>{{formatDate(account.PaymentDueDate)}}</li>
            </ul>
          </div>     
        </section>
        <section class="account-column grid" id="overdue-account-column">
          <div class="account-container-title" id="overdue-account-container-title">
            <h3>Overdue</h3>
          </div>
          <div class="account-container overdue-account"  v-for="account in overdueAccounts" v-bind:key="account.Id">
            <ul class="account-data-list">
              <li><label>Name:</label>{{account.LastName}}, {{account.FirstName}}</li>
              <li><label>Email:</label>{{account.Email}}</li>
              <li><label>Phone Number:</label>{{account.PhoneNumber.replace(/(\d{3})(\d{3})(\d{4})/, '($1)-$2-$3')}}</li>
              <li><label>Amount Due:</label>${{account.AmountDue}}</li>
              <li><label>Due Date:</label>{{formatDate(account.PaymentDueDate)}}</li>
            </ul>
          </div>
        </section>
        <div class="account-column grid" id="inactive-account-column">
          <div class="account-container-title" id="inactive-account-container-title">
            <h3>Inactive</h3>
          </div>
          <div class="account-container inactive-account" v-for="account in inactiveAccounts" v-bind:key="account.Id">
            <ul class="account-data-list">
              <li><label>Name:</label>{{account.LastName}}, {{account.FirstName}}</li>
              <li><label>Email:</label>{{account.Email}}</li>
              <li><label>Phone Number:</label>{{account.PhoneNumber.replace(/(\d{3})(\d{3})(\d{4})/, '($1)-$2-$3')}}</li>
              <li><label>Amount Due:</label>${{account.AmountDue}}</li>
              <!-- Due date is removed as inactive accounts are the only types without payment due dates. A property could be made
              in the case that an active or overdue account does not have a due date to hide it -->
            </ul>
          </div>
        </div>
      </section>
    </main>
  </div>
</template>

<script>
import moment from 'moment'; // used for date formatting
import axios from 'axios'; // I got a bit of help from Deven and Joe on using axios

export default{
  name: 'app',
  data () {
    return {
      accounts: [],
    }
  },
  mounted () {
    axios
      .get('https://frontiercodingtests.azurewebsites.net/api/accounts/getall')
      .then(response => (this.accounts = response.data))
  },
  
  methods: {
    formatDate: function (date) {
      return moment(date, 'YYYY-MM-DD').format('DD/MM/YYYY');
    }
  },

  computed: {
    activeAccounts: function() {
      return this.accounts.filter(function(account) {
        return account.AccountStatusId == 0;
      });
    },
    inactiveAccounts: function() {
      return this.accounts.filter(function(account) {
        return account.AccountStatusId == 1;
      });
    },
    overdueAccounts: function() {
      return this.accounts.filter(function(account) {
        return account.AccountStatusId == 2;
      });
    },
  }
}
</script>
