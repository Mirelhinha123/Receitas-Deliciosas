<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

// O 'useRouter' nos permite navegar entre páginas
const router = useRouter()

// --- Estado do Formulário ---
// Usamos 'ref' para armazenar os dados de cada campo
const recipeName = ref('')
const category = ref('Almoço') // Valor padrão da imagem
const prepTime = ref(30) // Valor padrão da imagem
const difficulty = ref('Médio') // Valor padrão da imagem
const ingredients = ref('')
const instructions = ref('')
const tips = ref('')
const imageFile = ref(null)
const imageUrl = ref('')

// --- Opções dos Dropdowns ---
const categories = ref([
  'Almoço',
  'Jantar',
  'Café da Manhã',
  'Sobremesa',
  'Salada',
  'Lanche'
])
const difficulties = ref(['Fácil', 'Médio', 'Difícil'])

// --- Métodos (Funções) ---

/**
 * Lida com o envio do formulário.
 */
function handleSubmit() {
  // Constrói um objeto com todos os dados
  const newRecipe = {
    name: recipeName.value,
    category: category.value,
    prepTime: prepTime.value,
    difficulty: difficulty.value,
    ingredients: ingredients.value.split('\n'), // Transforma texto em array
    instructions: instructions.value.split('\n'), // Transforma texto em array
    tips: tips.value.split('\n').filter(tip => tip.trim() !== ''), // Opcional
    imageFile: imageFile.value, // O arquivo em si (para upload)
    imageUrl: imageUrl.value // A URL (se fornecida)
  }

  // Por enquanto, vamos apenas exibir os dados no console
  console.log('Enviando nova receita:', newRecipe)

  // Aqui você enviaria os dados para uma API
  
  // Após enviar, redireciona para a home
  alert('Receita enviada com sucesso! (Verifique o console)')
  router.push('/')
}

/**
 * Lida com o clique no botão "Cancelar".
 * Limpa o formulário e volta para a página inicial.
 */
function handleCancel() {
  // Você pode adicionar uma confirmação se quiser
  if (confirm('Tem certeza que deseja cancelar? Os dados não salvos serão perdidos.')) {
    router.push('/') // Redireciona para a página inicial
  }
}

/**
 * Armazena o arquivo selecionado no estado.
 */
function handleFileUpload(event) {
  const file = event.target.files[0]
  if (file) {
    imageFile.value = file
    imageUrl.value = '' // Limpa o campo URL se um arquivo for selecionado
  }
}

/**
 * Limpa o campo de arquivo se uma URL for digitada.
 */
function handleUrlInput() {
  if (imageUrl.value) {
    imageFile.value = null
    // Limpa o input de arquivo (um pouco mais complexo, mas opcional)
    const fileInput = document.getElementById('fileUpload')
    if (fileInput) fileInput.value = null
  }
}
</script>

<template>
    <nav class="nav">
      <router-link to="/" class="logo-link">
        <img src="/logorec.png" alt="Logo do site" class="logo" />
        Receitas Deliciosas
      </router-link>
        <router-link to="/" exact-active-class="active">
          <span class="icon">🏠</span>
          Início
        </router-link>
        <router-link to="/receitas" active-class="active">
          <span class="icon">🍽️</span>
          Receitas
        </router-link>
        <router-link to="/favorites" active-class="active">
          <span class="icon">❤️</span>
          Favoritos
        </router-link>
        <router-link to="/add" active-class="active">
          <span class="icon">➕</span>
          Adicionar
        </router-link>
        <div class="search-nav" :class="{ 'open': isSearchOpen }">
          <input v-model="q" placeholder="Buscar receitas..." @keyup.enter="doSearch" aria-label="Pesquisar receitas" />
          <button @click="toggleSearch" aria-label="Abrir busca">🔍</button>
        </div>
      </nav>
  <div class="add-recipe-container container">
    <h2>Enviar Nova Receita</h2>
    <p class="subtitle">Compartilhe sua receita com a comunidade</p>

    <form class="add-recipe-form" @submit.prevent="handleSubmit">
      
      <div class="form-group">
        <label for="name">Nome da Receita *</label>
        <input
          type="text"
          id="name"
          v-model="recipeName"
          placeholder="Ex: Bolo de Chocolate"
          required
        />
      </div>

      <div class="form-group">
        <label for="category">Categoria *</label>
        <select id="category" v-model="category" required>
          <option v-for="cat in categories" :key="cat" :value="cat">
            {{ cat }}
          </option>
        </select>
      </div>

      <div class="form-group">
        <label for="prepTime">Tempo de Preparo (minutos) *</label>
        <input
          type="number"
          id="prepTime"
          v-model.number="prepTime"
          min="1"
          required
        />
      </div>

      <div class="form-group">
        <label for="difficulty">Dificuldade *</label>
        <select id="difficulty" v-model="difficulty" required>
          <option v-for="diff in difficulties" :key="diff" :value="diff">
            {{ diff }}
          </option>
        </select>
      </div>

      <div class="form-group">
        <label for="ingredients">Ingredientes *</label>
        <textarea
          id="ingredients"
          v-model="ingredients"
          placeholder="Liste os ingredientes, um por linha&#10;Ex:&#10;500g de farinha&#10;3 ovos&#10;200ml de leite"
          rows="7"
          required
        ></textarea>
        <small>Digite cada ingrediente em uma linha separada</small>
      </div>

      <div class="form-group">
        <label for="instructions">Modo de Preparo *</label>
        <textarea
          id="instructions"
          v-model="instructions"
          placeholder="Descreva o passo a passo, um por linha&#10;Ex:&#10;Pré-aqueça o forno a 180°C&#10;Misture os ingredientes secos"
          rows="9"
          required
        ></textarea>
        <small>Digite cada passo em uma linha separada</small>
      </div>

      <div class="form-group">
        <label for="tips">Dicas Adicionais (Opcional)</label>
        <textarea
          id="tips"
          v-model="tips"
          placeholder="Compartilhe dicas úteis, uma por linha&#10;Ex:&#10;Use ingredientes em temperatura ambiente"
          rows="4"
        ></textarea>
        <small>Digite cada dica em uma linha separada</small>
      </div>

      <div class="form-group">
        <label for="fileUpload">Upload de Imagem (Opcional)</label>
        <input
          type="file"
          id="fileUpload"
          @change="handleFileUpload"
          accept="image/png, image/jpeg"
        />
        <small>Selecione um arquivo de imagem do seu computador (PNG/JPG).</small>
      </div>

      <div class="form-group">
        <label for="imageUrl">Ou URL da Imagem (Opcional)</label>
        <input
          type="url"
          id="imageUrl"
          v-model="imageUrl"
          @input="handleUrlInput"
          placeholder="https://exemplo.com/imagem.jpg"
        />
        <small>Cole a URL de uma imagem da sua receita (ou use o upload acima).</small>
      </div>

      <p class="form-note">* Campos obrigatórios</p>

      <div class="form-actions">
        <button type="button" class="btn secondary" @click="handleCancel">
          Cancelar
        </button>
        <button type="submit" class="btn">
          Enviar Receita
        </button>
      </div>
    </form>
  </div>
</template>

<style scoped>
/* A maior parte dos estilos (inputs, botões, .add-recipe-form) 
  já deve estar no seu CSS global. 
  Estes são apenas estilos específicos para esta página.
*/

.add-recipe-container {
  max-width: 800px;
  margin: 2rem auto;
  padding-bottom: 4rem;
}

h2 {
  font-size: 2rem;
  font-weight: 700;
  margin-bottom: 0.5rem;
  color: #333;
}

.subtitle {
  font-size: 1.1rem;
  color: var(--muted);
  margin-bottom: 2rem;
}

.form-group {
  margin-bottom: 1.5rem;
}

.form-group label {
  display: block;
  margin-bottom: 8px;
  font-weight: 600;
  color: #555;
}

/* O estilo de input, select e textarea é pego do CSS global 
  (que você me passou antes) 
*/

.form-group small {
  display: block;
  margin-top: 6px;
  font-size: 0.9rem;
  color: var(--muted);
}

.form-note {
  font-size: 0.9rem;
  color: var(--muted);
  margin-top: 1.5rem;
  padding-bottom: 1rem;
  border-bottom: 1px solid #eee;
}

.form-actions {
  display: flex;
  justify-content: flex-end;
  gap: 1rem;
  margin-top: 1.5rem;
}

/* Estilo para input[type=file] */
input[type="file"] {
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: var(--border-radius-sm);
  background: #f9f9f9;
}

input[type="file"]::file-selector-button {
  background: var(--orange);
  color: white;
  border: 0;
  padding: 8px 12px;
  border-radius: var(--border-radius-sm);
  cursor: pointer;
  transition: var(--transition);
  margin-right: 12px;
}

input[type="file"]::file-selector-button:hover {
  background: #e66a00;
}
</style>