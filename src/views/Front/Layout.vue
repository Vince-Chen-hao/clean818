<template>
  <div>
    <Navbar />
    <Alert></Alert>
    <router-view></router-view>
    <Footer></Footer>
  </div>
</template>

<script>
import Alert from '../../components/Share/Alert.vue';
import Footer from '../../components/Front/Footer.vue';
import Navbar from '../../components/Front/Navbar.vue';

export default {
  components: {
    Navbar,
    Alert,
    Footer,
  },
  data() {
    return {};
  },
};
</script>
