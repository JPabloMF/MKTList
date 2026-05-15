<template>
    <div class="container py-4">

        <!-- Add Recipe -->
        <div class="input-group mb-4">
            <input type="text" class="form-control" placeholder="Recipe name" v-model="newRecipeName" @keyup.enter="addRecipe" />
            <button @click="addRecipe" class="btn btn-primary">Add Recipe</button>
        </div>

        <!-- Recipe List -->
        <div class="accordion" id="recipeAccordion">
            <div class="accordion-item" v-for="(recipe, index) in recipes" :key="recipe.name">
                <h2 class="accordion-header d-flex align-items-center">
                    <button
                        class="accordion-button collapsed flex-grow-1"
                        type="button"
                        :data-bs-target="'#recipe-' + index"
                        data-bs-toggle="collapse"
                    >
                        {{ recipe.name }}
                        <span class="badge bg-secondary ms-2">{{ recipe.ingredients.length }}</span>
                    </button>
                    <button @click="removeRecipe(index)" class="btn btn-danger btn-sm me-2" title="Remove recipe">&times;</button>
                </h2>
                <div :id="'recipe-' + index" class="accordion-collapse collapse" data-bs-parent="#recipeAccordion">
                    <div class="accordion-body">

                        <!-- Ingredients Table -->
                        <table class="table table-sm mb-3" v-if="recipe.ingredients.length">
                            <thead>
                                <tr>
                                    <th>Ingredient</th>
                                    <th>Qty</th>
                                    <th>Price</th>
                                    <th>Total</th>
                                    <th></th>
                                </tr>
                            </thead>
                            <tbody>
                                <tr v-for="(ingredient, iIndex) in recipe.ingredients" :key="ingredient.name">
                                    <td :class="{ 'text-success': ingredient.bought }">
                                        <input type="checkbox" class="form-check-input me-2" v-model="ingredient.bought" />
                                        {{ ingredient.name }}
                                    </td>
                                    <td :class="{ 'text-success': ingredient.bought }">{{ ingredient.quantity }}</td>
                                    <td :class="{ 'text-success': ingredient.bought }">${{ ingredient.price.toFixed(2) }}</td>
                                    <td :class="{ 'text-success': ingredient.bought }">${{ (ingredient.quantity * ingredient.price).toFixed(2) }}</td>
                                    <td>
                                        <button @click="removeIngredient(recipe, iIndex)" class="btn btn-danger btn-sm py-0" title="Remove ingredient">&times;</button>
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                        <p class="text-secondary small" v-else>No ingredients yet.</p>

                        <!-- Add Ingredient -->
                        <div class="row g-2">
                            <div class="col-12 col-md-4">
                                <input type="text" class="form-control form-control-sm" placeholder="Ingredient" v-model="newIngredientName" />
                            </div>
                            <div class="col-6 col-md-3">
                                <input type="number" class="form-control form-control-sm" placeholder="Qty" v-model.number="newIngredientQuantity" min="0" />
                            </div>
                            <div class="col-6 col-md-3">
                                <input type="number" class="form-control form-control-sm" placeholder="Price" v-model.number="newIngredientPrice" min="0" step="0.01" />
                            </div>
                            <div class="col-12 col-md-2">
                                <button @click="addIngredient(recipe)" class="btn btn-success btn-sm w-100">Add</button>
                            </div>
                        </div>

                    </div>
                </div>
            </div>
        </div>

        <p class="text-center text-secondary mt-4" v-if="!recipes.length">No recipes yet. Add one above.</p>

    </div>
</template>

<script>
export default {
    name: 'List',
    data() {
        return {
            newRecipeName: '',
            newIngredientName: '',
            newIngredientQuantity: 0,
            newIngredientPrice: 0,
            recipes: JSON.parse(localStorage.getItem('mktlist-recipes') || '[]')
        }
    },
    watch: {
        recipes: {
            deep: true,
            handler(val) {
                localStorage.setItem('mktlist-recipes', JSON.stringify(val));
            }
        }
    },
    methods: {
        addRecipe() {
            if (this.newRecipeName.trim() !== '') {
                this.recipes.push({
                    name: this.newRecipeName,
                    ingredients: []
                });
                this.newRecipeName = '';
            }
        },
        addIngredient(recipe) {
            if (this.newIngredientName.trim() !== '' && this.newIngredientQuantity > 0 && this.newIngredientPrice > 0) {
                recipe.ingredients.push({
                    name: this.newIngredientName,
                    quantity: this.newIngredientQuantity,
                    price: this.newIngredientPrice,
                    bought: false
                });
                this.newIngredientName = '';
                this.newIngredientQuantity = 0;
                this.newIngredientPrice = 0;
            }
        },
        removeRecipe(index) {
            this.recipes.splice(index, 1);
        },
        removeIngredient(recipe, index) {
            recipe.ingredients.splice(index, 1);
        }
    }
}
</script>
