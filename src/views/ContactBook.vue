<template>
  <div class="page row">
    <div class="col-md-10">
      <InputSearch v-model="searchText" />
    </div>
    <div class="mt-3 col-md-6">
      <h4>AddressBook <i class="fas fa-address-book"></i></h4>
      <ContactList
        v-if="filteredContactsCount > 0"
        :contacts="filteredContacts"
        v-model:activeIndex="activeIndex"
      />
      <p v-else>Khong co lien he</p>

      <div class="mt-3 row justify-content-around align-items-center">
        <button class="col-3 btn btn-sm btn-primary" @click="refreshList()">
          <i class="fas fa-redo"></i> Lam moi
        </button>
        <button class="col-3 btn btn-sm btn-success" @click="goToAddContact">
          <i class="fas fa-plus"></i> Them moi
        </button>
        <button class="col-3 btn btn-sm btn-danger" @click="removeAllContacts">
          <i class="fas fa-trash"></i> Xoa tat ca
        </button>
      </div>
    </div>

    <div class="mt-3 col-md-6">
      <div v-if="activeContact">
        <h4>Chi tiet lien he <i class="fas fa-address-card"></i></h4>
        <ContactCard :contact="activeContact" />
      </div>
    </div>
  </div>
</template>

<script>
import InputSearch from "../components/InputSearch.vue";
import ContactList from "../components/ContactList.vue";
import ContactCard from "../components/ContactCard.vue";
import ContactService from "../services/contact.service";

export default {
  components: {
    ContactCard,
    InputSearch,
    ContactList,
  },
  data() {
    return {
      contacts: [],
      activeIndex: -1,
      searchText: "",
    };
  },
  watch: {
    searchText() {
      this.activeIndex = -1;
    },
  },
  computed: {
    contactStrings() {
      return this.contacts.map((conatct) =>
        this.contactStrings[index].includes(this.searchText)
      );
    },
  filteredContacts(){
    if(!this.searchText) return this.contacts;
    return this.contacts.filter((_contact, index)=>
    this.contactStrings[index].includes(this.searchText))
  },
  activeContact() {
    if (this.activeIndex < 0) return null;
    return this.filteredContacts[this.activeIndex];
  },
  filteredContactsCount() {
    console.log(this.filteredContacts);
    return this.filteredContacts.length;
  },
},
  methods: {
    async retrieveContacts() {
      try {
        this.contacts = await ContactService.getAll();
      } catch (error) {
        console.log(error);
      }
    },

    refreshList() {
      this.retrieveContacts();
      this.activeIndex = -1;
    },

    async removeAllContacts() {
      if (confirm("Ban muon xoa tat ca lien he?")) {
        try {
          await ContactService.deleteAll();
          this.refreshList();
        } catch (error) {
            console.log(error);
        }
      }
    },

    goToAddContact(){
        this.$router.push({name: "contact.add"});
    },
  },

  mounted(){
    this.refreshList();
  },
};
</script>

<style scoped>
.page {
  text-align: left;
  max-width: 1000px;
}
</style>
