   <template>
     <div class="down-button" :class="transformButton(index)">
       <q-btn
         flat
         round
         color="primary"
         padding="none"
         size="md"
         icon="mdi-chevron-down"
         @click="
           currentCategoryIndex.value === index && isBannerVisible.value
             ? hideBanner()
             : showBanner(index)
         "
       />
     </div>
   </template>

   <script>
   // ... ваш код ...
 
   methods: {
     // ... ваш код ...
 
     showBanner(index) {
       isBannerVisible.value = true; // Используем `value` для изменения ref
       currentCategoryIndex.value = index;
     },
     
     hideBanner() {
       isBannerVisible.value = false; // Используем `value` для изменения ref
     },
     // ... ваш код ...
   }
   </script>
   
