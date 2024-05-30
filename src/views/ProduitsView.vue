<template>
    <main>
        <div>
            <!-- Tableau pour afficher la liste des produits -->
            <table>
                <caption>Liste des produits</caption>
                <tr>
                    <th>Nom</th>
                    <th>Prix unitaire</th>
                    <th>Unités en stock</th>
                    <th>Unites commandées</th>
                </tr>
                <!-- Si le tableau des produits est vide -->
                <tr v-if="produits.length === 0">
                    <td colspan="4">Veuillez patienter, chargement des produits...</td>
                </tr>
                <!-- Boucle sur les produits -->
                <tr v-for="produit in produits" :key="produit.reference">
                    <td>{{ produit.nom }}</td>
                    <td>{{ produit.prixUnitaire }}</td>
                    <td>{{ produit.unitesEnStock }}</td>
                    <td>{{ produit.unitesCommandees }}</td>
                </tr>
            </table>
        </div>
        <!-- Boutons de pagination -->
        <div>
            <button @click="chargerProduits(0, 5)" :disabled="pageInfo.number === 0">Début</button>
            <button @click="chargerProduits(pageInfo.number - 1, 5)" :disabled="pageInfo.number === 0">Page précédente</button>
            <button @click="chargerProduits(pageInfo.number + 1, 5)" :disabled="pageInfo.number === pageInfo.totalPages - 1">Page suivante</button>
            <button @click="chargerProduits(pageInfo.totalPages - 1, 5)" :disabled="pageInfo.number === pageInfo.totalPages - 1">Fin</button>
        </div>
    </main>
</template>

<script setup>
import { reactive, onMounted, ref } from "vue";
import { doAjaxRequest } from "@/api";

let produits = ref([]);
let pageInfo = ref([]);

// Fonction pour afficher les erreurs
function showError(error) {
    console.error("Erreur : status %d", error.status);
    console.error(error.body);
    alert(error.message);
}

// Fonction pour charger les catégories de la page spécifiée
async function chargerProduits(page, size) {
    doAjaxRequest("/api/produits?page=" + page + "&size=" + size)
        .then((json) => {
            produits.value = json._embedded.produits;
            pageInfo.value = json.page
            console.log(produits.value);
            console.log(pageInfo.value);
        })
        .catch(showError);
}

// Au montage du composant, charger la première page de catégories
onMounted(async () => {
    chargerProduits(0, 5);
});
</script>

<style scoped>
td,
th {
    border: 1px solid #ddd;
    padding: 8px;
}

th {
    padding-top: 12px;
    padding-bottom: 12px;
    text-align: left;
    background-color: #232623;
    color: rgb(255, 255, 255);
}
</style>