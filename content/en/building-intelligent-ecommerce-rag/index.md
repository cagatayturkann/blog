---
title: 'Building Intelligent E-commerce Experiences with RAG'
summary: In this article, we will explore how Retrieval-Augmented Generation (RAG) can transform e-commerce applications. We'll discuss the implementation of vector databases, semantic search, conversation memory, and multi-language support through a practical example project.
date: 2025-03-14T10:00:00+03:00
cardimage: '/blog/en/building-intelligent-ecommerce-rag/images/cover.svg'
featureimage: '/blog/en/building-intelligent-ecommerce-rag/images/cover.svg'
featured: true
categories: ['AI']
tags: ['rag', 'vectordb', 'nodejs', 'ai']
translationKey: 'ecommerce-rag-implementation'
authors:
  - Çağatay: /images/author.jpeg
---

# Building Intelligent E-commerce Experiences with RAG

## Introduction

E-commerce platforms are constantly evolving to provide more personalized and efficient shopping experiences. Traditional search and recommendation systems often fall short when it comes to understanding the nuanced intent behind customer queries. This is where Retrieval-Augmented Generation (RAG) comes in, combining the power of large language models with precise information retrieval to create more intelligent shopping assistants.

In this article, we'll explore how to implement a RAG-based e-commerce assistant that can:

- Understand and respond to complex product queries
- Maintain conversation context for follow-up questions
- Support multiple languages seamlessly
- Provide accurate product information from a vector database
- Generate human-like responses while avoiding hallucinations

You can access the complete code for this project at [GitHub repository](https://github.com/cagatayturkann/ecommerceRAG).
You can test the assistant from here [Assistant](https://ecommerce-rag.vercel.app/)

## 1. Understanding RAG for E-commerce

### What is RAG?

Retrieval-Augmented Generation (RAG) is an AI architecture that enhances large language models (LLMs) by retrieving relevant information from external knowledge sources before generating responses. Unlike traditional LLMs that rely solely on their pre-trained knowledge, RAG systems can access up-to-date, domain-specific information.

The RAG process consists of three main steps:

1. **Retrieval**: When a user asks a question, the system searches a database for relevant information
2. **Augmentation**: The retrieved information is added to the prompt sent to the LLM
3. **Generation**: The LLM generates a response based on both its pre-trained knowledge and the retrieved information

### Why RAG is Perfect for E-commerce

E-commerce presents unique challenges that make RAG particularly valuable:

- **Product Catalogs Change Frequently**: New products are added, prices change, and inventory fluctuates
- **Specific Product Details Matter**: Customers need accurate information about specifications, compatibility, and features
- **Query Intent is Complex**: "Show me a laptop good for gaming under $1000" requires understanding multiple constraints
- **Follow-up Questions are Common**: "Does it have a backlit keyboard?" requires maintaining context from previous questions

Traditional search systems match keywords but struggle with semantic understanding. RAG bridges this gap by combining semantic search with the conversational abilities of LLMs.

### RAG vs. Traditional Search in E-commerce

| Feature | Traditional Search | RAG-Based Search |
| ------- | ------------------ | ---------------- |
| Query Understanding | Keyword matching | Semantic understanding |
| Result Relevance | Based on text similarity | Based on meaning and context |
| Handling Ambiguity | Limited | Strong |
| Follow-up Questions | Requires new search | Maintains context |
| Response Format | List of products | Conversational with product details |
| Product Knowledge | Limited to indexed fields | Can include all product details |

## 2. Vector Databases and Embeddings

### The Role of Vector Databases

Vector databases are specialized storage systems designed to efficiently store and query high-dimensional vectors (embeddings). In our e-commerce RAG system, they serve as the foundation for semantic search.

Key features of vector databases include:

- **Similarity Search**: Finding vectors that are semantically similar to a query vector
- **Efficient Indexing**: Algorithms like HNSW (Hierarchical Navigable Small World) for fast retrieval
- **Hybrid Search**: Combining vector similarity with traditional filters
- **Metadata Storage**: Keeping product information alongside vectors

For our e-commerce RAG project, we chose Weaviate as our vector database due to its robust features, GraphQL API, and hybrid search capabilities.

### Creating Product Embeddings

Embeddings are numerical representations of text that capture semantic meaning. In our system, we generate embeddings for product information using Google's text-embedding-004 model.

Here's how we implement the embedding generation in our project:

```javascript
const { GoogleGenerativeAI } = require('@google/generative-ai');
require('dotenv').config();

// Get API key from environment variables
const GEMINI_API_KEY = process.env.GEMINI_API_KEY;

// Initialize API client
const genAI = GEMINI_API_KEY ? new GoogleGenerativeAI(GEMINI_API_KEY) : null;

/**
 * Generates an embedding vector for the given text
 * @param {String} text - Text to generate embedding for
 * @returns {Array} - Embedding vector
 */
async function getEmbedding(text) {
  const model = genAI.getGenerativeModel({ model: 'text-embedding-004' });
  const result = await model.embedContent(text);
  return result.embedding.values;
}
```

These embeddings allow us to convert product descriptions, titles, and features into vectors that can be compared for semantic similarity.

### Implementing Vector Search

With our products embedded in the vector database, we can now implement semantic search. The following code demonstrates how we retrieve relevant products based on a user query:

```javascript
/**
 * Retrieves product information from Weaviate vector database
 * @param {String} query - Search query
 * @returns {Array} - Product information
 */
const getProductInfo = async (query) => {
  try {
    const embedding = await getEmbedding(query);

    // Use fixed alpha value for vector search
    const alpha = 0.5;

    // Clean query text for GraphQL - make it safe
    const cleanQuery = query
      .substring(0, 100) // Keep query at reasonable length
      .replace(/[^\w\s]/gi, '') // Remove non-alphanumeric characters
      .replace(/"/g, '') // Remove double quotes
      .trim();

    const data = JSON.stringify({
      query: `{
        Get {
          Ecommerce (
            hybrid: {
              query: "${cleanQuery}"
              alpha: ${alpha},
              vector: ${JSON.stringify(embedding)},
            }
            limit: 3
          ) {
            data
            _additional { score }
          }
        }
      }`,
    });

    const config = {
      method: 'post',
      url: process.env.WEAVIATE_URL + '/v1/graphql',
      headers: {
        'Content-Type': 'application/json',
        Authorization: `Bearer ${process.env.WEAVIATE_API_KEY}`,
      },
      data: data,
    };

    const response = await axios.request(config);
    return response.data.data.Get.Ecommerce.map((item) => item.data);
  } catch (error) {
    console.error('Error during Weaviate query:', error);
    return [];
  }
};
```

The `hybrid` parameter in our query allows us to combine vector search (semantic similarity) with keyword matching. The `alpha` value (0.5) gives equal weight to both approaches, providing a balance between semantic understanding and keyword relevance.

## 3. Conversation Management and Context

### Maintaining Conversation History

One of the key challenges in building an effective e-commerce assistant is maintaining conversation context. Users often ask follow-up questions that reference previous queries or responses.

In our project, we use MongoDB to store conversation history. Each conversation is stored as a document with an array of messages:

```javascript
/**
 * Creates a new conversation
 * @param {String} title - Conversation title (optional)
 * @returns {Promise<Object>} - Created conversation
 */
const createConversation = async (title = null) => {
  try {
    // Assign default title if null
    const conversationTitle = title || 'New Conversation';
    
    const conversation = new Conversation({
      title: conversationTitle,
      messages: []
    });
    
    await conversation.save();
    return conversation;
  } catch (error) {
    console.error('Error creating conversation:', error);
    throw error;
  }
};

/**
 * Adds a message to a conversation
 * @param {String} conversationId - Conversation ID
 * @param {String} role - Message role (user/assistant)
 * @param {String} content - Message content
 * @returns {Promise<Object>} - Updated conversation
 */
const addMessageToConversation = async (conversationId, role, content) => {
  try {
    const conversation = await Conversation.findById(conversationId);
    if (!conversation) {
      throw new Error(`Conversation not found with ID: ${conversationId}`);
    }
    
    conversation.messages.push({
      role,
      content,
      timestamp: new Date()
    });
    
    conversation.updatedAt = new Date();
    await conversation.save();
    return conversation;
  } catch (error) {
    console.error('Error adding message to conversation:', error);
    throw error;
  }
};
```

### Detecting Follow-up Questions

To provide a seamless conversation experience, our system needs to detect when a user is asking a follow-up question. We use a dedicated classifier agent for this purpose:

```javascript
/**
 * Determines if a message is a follow-up question based on conversation history
 * @param {Array} conversationHistory - Array of previous conversation messages
 * @returns {String} - JSON string indicating if message is a follow-up (true/false)
 */
const classifierAgent = async (conversationHistory) => {
  try {
    // Format conversation history for the AI model
    const formattedHistory = conversationHistory
      .map((msg) => `${msg.role === 'user' ? 'User' : 'Assistant'}: ${msg.content}`)
      .join('\n');

    const response = await axios({
      method: 'post',
      url: 'https://openrouter.ai/api/v1/chat/completions',
      headers: {
        Authorization: `Bearer ${process.env.OPENROUTER_API_KEY}`,
        'Content-Type': 'application/json',
      },
      data: {
        model: 'google/gemini-2.0-flash-001',
        messages: [
          {
            role: 'system',
            content: `You are a sophisticated AI agent specializing in determining if a user's message is a follow-up question to a previous conversation. A follow-up question is a question that refers to or builds upon a previous question or response in the conversation. For example, if a user asks about a product and then asks about its price, the second question is a follow-up question.

            Analyze the conversation history and determine if the last user message is a follow-up question. Return only a JSON boolean value (true or false) as plain text.`,
          },
          {
            role: 'user',
            content: `Here is the conversation history:\n${formattedHistory}\n\nIs the last user message a follow-up question?`,
          },
        ],
      },
    });

    return response.data.choices[0].message.content.replace(/\n+$/, '');
  } catch (error) {
    console.error('OpenRouter API error:', error);
    return 'false';
  }
};
```

When a follow-up question is detected, we expand the search query to include context from previous messages:

```javascript
// If it's a follow-up question, include previous user questions
if (isFollowUp && conversationHistory.length >= 3) {
  // Find previous user messages (last 2 user messages)
  const userMessages = conversationHistory.filter((msg) => msg.role === 'user').slice(-2);

  if (userMessages.length >= 2) {
    const previousUserMessage = userMessages[0].content; // Previous user message
    searchQuery = `${await translatorAgent(previousUserMessage)} ${translatedMessage}`;
    console.log(`Expanded search query (follow-up): "${searchQuery}"`);
  }
}
```

This approach ensures that when a user asks something like "What about its battery life?" after asking about a specific laptop, our system understands the context and provides relevant information.

## 4. Multi-language Support

### Automatic Language Detection and Translation

To make our e-commerce assistant accessible to a global audience, we implemented automatic language detection and translation. This allows users to interact with the system in their preferred language while maintaining the semantic search capabilities.

We use a translator agent to convert user queries to English for processing:

```javascript
/**
 * Translates user messages to English
 * @param {String} message - User message to translate
 * @returns {String} - Translated message in English
 */
const translatorAgent = async (message) => {
  try {
    const response = await axios({
      method: 'post',
      url: 'https://openrouter.ai/api/v1/chat/completions',
      headers: {
        Authorization: `Bearer ${process.env.OPENROUTER_API_KEY}`,
        'Content-Type': 'application/json',
      },
      data: {
        model: 'google/gemini-2.0-flash-001',
        messages: [
          {
            role: 'system',
            content: `You are a sophisticated AI agent specializing in translating user queries from multiple languages into English. Before translating, you identify and correct typos, grammatical errors, and punctuation mistakes in the source text to ensure clarity and readability. Your translations should maintain the original context and be culturally nuanced. Return only the translated text as plain text.`,
          },
          {
            role: 'user',
            content: message,
          },
        ],
      },
    });

    return response.data.choices[0].message.content.replace(/\n+$/, '');
  } catch (error) {
    console.error('OpenRouter API error:', error);
    return message; // Fallback to original message
  }
};
```

The system then responds in the same language as the user's query. This is handled by our AI service, which is instructed to match the language of the input:

```javascript
const messageContent = `
  You are a helpful shopping assistant that can answer questions about products. Based on the user's question "${prompt}", analyze the product information in the context: ${context} and return information only about the most relevant matching product(s). Answer questions related to the ${category} of the product(s). Answer as humanly as possible and in the same language as the ${prompt}, but be careful with special names such as brand, model, etc. - don't translate those.
  
  // ... additional instructions ...
`;
```

### Language-Aware Product Information

When displaying product information, we ensure that the system maintains the user's language preference. The AI generates descriptions in the user's language, while preserving brand names, model numbers, and technical specifications.

For example, if a user asks in Turkish: "Oyun için iyi bir laptop önerir misiniz?", the system will:

1. Translate the query to English: "Can you recommend a good laptop for gaming?"
2. Perform semantic search using the English query
3. Retrieve relevant gaming laptops from the vector database
4. Generate a response in Turkish, describing the gaming laptops while keeping technical terms intact

This approach provides a seamless multilingual experience without requiring separate product databases for each language.

## 5. AI Response Generation

### Contextual Response Generation

Once we've retrieved relevant product information, we need to generate a helpful, contextual response. We use the Gemini API for this purpose, with a fallback to OpenRouter if needed:

```javascript
/**
 * Generates a response using Gemini or falls back to OpenRouter API
 * @param {String} prompt - User message
 * @param {String} context - Product information or other context
 * @param {String} category - Message category
 * @param {Array} conversationHistory - Conversation history
 * @returns {String} - AI response
 */
const generateGeminiResponse = async (prompt, context, category, conversationHistory = []) => {
  try {
    // Format conversation history for the AI model
    const formattedHistory = conversationHistory
      .map((msg) => `${msg.role === 'user' ? 'User' : 'Assistant'}: ${msg.content}`)
      .join('\n');

    const messageContent = `
      You are a helpful shopping assistant that can answer questions about products. Based on the user's question "${prompt}", analyze the product information in the context: ${context} and return information only about the most relevant matching product(s). Answer questions related to the ${category} of the product(s). Answer as humanly as possible and in the same language as the ${prompt}, but be careful with special names such as brand, model, etc. - don't translate those.
      
      If the category is [PRODUCT_INFO] and the question is about general product information (like price, features, specifications etc), include [SHOW_PRODUCT_INFO] at the end of your response so that product information can be formatted and displayed to the user.
      
      If the question is specifically about product reviews, ratings or customer feedback, do not include [SHOW_PRODUCT_INFO]. Instead, focus on providing a summary of the reviews and ratings from the available data.

      Also consider follow up questions. If the user asks a follow up question about a product mentioned earlier, your answer should be about the SAME product. Do not switch to a different product unless the user explicitly asks about a new product.

      At the end of your response, include a JSON object with an array of product IDs that you referenced in your answer, in the format: [PRODUCT_IDS]{"ids":["id1","id2"]}[/PRODUCT_IDS]

      Here is the conversation history so far:
      ${formattedHistory}
      
      User: ${prompt}
      Assistant:
    `;

    try {
      // Use Gemini API
      const model = genAI.getGenerativeModel({
        model: 'gemini-2.0-flash',
        systemInstruction: messageContent,
      });

      const res = await model.generateContent(prompt);
      return res.response.text();
    } catch (geminiError) {
      // Fall back to OpenRouter API if Gemini fails
      console.log('Gemini API failed, trying OpenRouter API...');

      const openRouterResponse = await axios.post(
        'https://openrouter.ai/api/v1/chat/completions',
        {
          model: 'anthropic/claude-3-opus:beta',
          messages: [{ role: 'user', content: messageContent }],
        },
        {
          headers: {
            Authorization: `Bearer ${process.env.OPENROUTER_API_KEY}`,
            'Content-Type': 'application/json',
          },
        }
      );

      return openRouterResponse.data.choices[0].message.content;
    }
  } catch (error) {
    console.error('Error generating AI response:', error);
    return 'Sorry, an error occurred while generating a response: ' + error.message;
  }
};
```

### Product Information Display

To enhance the user experience, we format product information in a visually appealing way. The AI response includes a special tag `[SHOW_PRODUCT_INFO]` when product details should be displayed:

```javascript
/**
 * Formats product information into HTML for display
 * @param {Array} products - Array of product objects
 * @returns {String} - HTML formatted product information
 */
const formatProductInfo = (products) => {
  if (!products || products.length === 0) {
    return 'No relevant product information found.';
  }

  if (products.error) {
    return products.message;
  }

  let productsHTML = '<div class="products-container">';

  products.forEach((product) => {
    const parsedProduct = typeof product === 'string' ? JSON.parse(product) : product;

    productsHTML += `
      <div class="product-card">
        <div class="product-image">
          <img src="${parsedProduct.thumbnail || parsedProduct.images?.[0]}" 
               alt="${parsedProduct.title || 'Product Image'}" 
               loading="lazy">
        </div>
        <div class="product-info">
          <h3 class="product-title">${parsedProduct.title || 'Name not specified'}</h3>
          <p class="product-description">${parsedProduct.description || 'Description not specified'}</p>
          <div class="product-price-container">
            <span class="product-price">$${parsedProduct.price || 'Price not specified'}</span>
          </div>
          <a href="/product/${parsedProduct.id}" class="product-detail-button" target="_blank">
            More Details
          </a>
        </div>
      </div>
    `;
  });

  productsHTML += '</div>';
  return productsHTML;
};
```

The AI can also specify which products to display by including product IDs in its response:

```javascript
// Extract product IDs from AI response
const productIdsMatch = response.match(/\[PRODUCT_IDS\](.*?)\[\/PRODUCT_IDS\]/);

let productsToShow;
if (productIdsMatch) {
  // Get product IDs specified by AI
  const productIds = JSON.parse(productIdsMatch[1]).ids;

  // Filter products to only show those with specified IDs
  productsToShow = products.filter((product) => {
    const parsedProduct = typeof product === 'string' ? JSON.parse(product) : product;
    return productIds.includes(parsedProduct.id.toString());
  });

  // Remove ID tags from response
  response = response.replace(/\[PRODUCT_IDS\].*?\[\/PRODUCT_IDS\]/, '');
} else {
  // Show all products if no IDs specified
  productsToShow = products;
}

// Format product information and add to response
const productInfo = productService.formatProductInfo(productsToShow);
response = response + productInfo;
```

This approach allows the AI to provide a conversational response while also displaying structured product information when appropriate.

## 6. Message Categorization

### Understanding User Intent

To provide more relevant responses, we categorize user messages based on their intent. This helps the system determine how to respond and what information to include:

```javascript
/**
 * Categorizes user messages into predefined categories
 * @param {String} message - User message to categorize
 * @returns {String} - Category of the message
 */
const categorizerAgent = async (message) => {
  try {
    const response = await axios({
      method: 'post',
      url: 'https://openrouter.ai/api/v1/chat/completions',
      headers: {
        Authorization: `Bearer ${process.env.OPENROUTER_API_KEY}`,
        'Content-Type': 'application/json',
      },
      data: {
        model: 'google/gemini-2.0-flash-001',
        messages: [
          {
            role: 'system',
            content: `You are a sophisticated AI agent specializing in categorizing user queries into one of the following categories:
            [PRODUCT_INFO] - Questions about product information, features, specifications, price, etc.
            [PRODUCT_COMPARISON] - Questions comparing multiple products
            [PRODUCT_RECOMMENDATION] - Questions asking for product recommendations
            [PRODUCT_AVAILABILITY] - Questions about product availability
            [PRODUCT_REVIEWS] - Questions about product reviews or ratings
            [GENERAL_INQUIRY] - General questions not related to specific products
            [CUSTOMER_SERVICE] - Questions about customer service, shipping, returns, etc.
            [OTHER] - Any other type of question
            
            Return only the category as plain text.`,
          },
          {
            role: 'user',
            content: message,
          },
        ],
      },
    });

    return response.data.choices[0].message.content.replace(/\n+$/, '');
  } catch (error) {
    console.error('OpenRouter API error:', error);
    return '[GENERAL_INQUIRY]';
  }
};
```

The category is then used to tailor the AI's response. For example, if the category is `[PRODUCT_INFO]`, the system will include detailed product information in the response.

### Category-Based Response Customization

Different categories of questions require different types of responses. Our system adjusts its behavior based on the detected category:

- **[PRODUCT_INFO]**: Includes detailed product specifications and displays product cards
- **[PRODUCT_COMPARISON]**: Highlights differences between products
- **[PRODUCT_RECOMMENDATION]**: Suggests products based on user criteria
- **[PRODUCT_REVIEWS]**: Focuses on customer feedback and ratings
- **[GENERAL_INQUIRY]**: Provides general information without specific product details

This categorization helps the system provide more relevant and helpful responses to a wide range of user queries.

## 7. System Architecture and Implementation

### Clean Architecture Design

Our ecommerceRAG project follows a clean architecture pattern with clear separation of concerns:

- **Controllers**: Handle HTTP requests and responses
- **Services**: Contain business logic and external API interactions
- **Routes**: Define API endpoints
- **Utils**: Provide utility functions
- **Config**: Handle configuration settings
- **Middlewares**: Implement middleware functions

This modular design makes the codebase more maintainable, testable, and scalable.

### Main Components

The main components of our system include:

1. **Chat Controller**: Processes user messages and coordinates the response generation
2. **Vector Service**: Handles interactions with the Weaviate vector database
3. **AI Service**: Manages AI model interactions (Gemini and OpenAI)
4. **Product Service**: Provides product-related services
5. **Conversation Service**: Manages conversation persistence and retrieval
6. **Agent Controller**: Contains agent logic for translation, categorization, and classification

### Processing Flow

The main processing flow in our system is handled by the `processChat` function:

```javascript
/**
 * Processes chat messages and generates AI responses
 * @param {Object} req - Express request object
 * @param {Object} res - Express response object
 */
const processChat = async (req, res) => {
  try {
    let { message, conversationId } = req.body;

    if (!message) {
      return res.status(400).json({ error: 'Message is required' });
    }

    // Categorize the message
    const category = await categorizerAgent(message);

    // Get existing conversation or create new one
    let conversation;
    try {
      if (conversationId) {
        conversation = await conversationService.getConversationById(conversationId);
      } else {
        conversation = await conversationService.createConversation();
      }
    } catch (error) {
      // Create a new conversation in case of error
      conversation = await conversationService.createConversation();
    }

    // Add user message to conversation
    await conversationService.addMessageToConversation(conversation._id, 'user', message);

    // Get conversation history (last 4 messages)
    const conversationHistory = await conversationService.getConversationMessages(conversation._id, 4);

    // Translate message to English
    let translatedMessage = await translatorAgent(message);

    // Determine if message is a follow-up question
    let isFollowUp = false;
    if (conversationHistory.length >= 3) {
      // Use classifierAgent to determine if it's a follow-up
      const response = await classifierAgent(conversationHistory);
      const parsedResponse = JSON.parse(response);
      isFollowUp = parsedResponse;
    }

    // Prepare search query
    let searchQuery = translatedMessage;
    if (isFollowUp && conversationHistory.length >= 3) {
      // Find previous user messages (last 2 user messages)
      const userMessages = conversationHistory.filter((msg) => msg.role === 'user').slice(-2);
      if (userMessages.length >= 2) {
        const previousUserMessage = userMessages[0].content; // Previous user message
        searchQuery = `${await translatorAgent(previousUserMessage)} ${translatedMessage}`;
      }
    }

    // Get product information from Weaviate
    const products = await vectorService.getProductInfo(searchQuery);

    // Process response based on product information
    let response;
    if (products && products.length > 0) {
      // Send product information to AI in JSON format
      const productContext = JSON.stringify(products);

      // Send category and conversation history to AI
      response = await aiService.generateGeminiResponse(message, productContext, category, conversationHistory);
      
      // Clean [SHOW_PRODUCT_INFO] tag
      const cleanedResponse = response.replace('[SHOW_PRODUCT_INFO]', '');
      await conversationService.addMessageToConversation(conversation._id, 'assistant', cleanedResponse);

      // Check if product information should be displayed
      if (response.includes('[SHOW_PRODUCT_INFO]')) {
        // Process and display product information
        // ... (code omitted for brevity)
      }
    } else {
      // If no product information found
      const productInfo = JSON.stringify({ message: 'No relevant product information found.' });
      response = await aiService.generateGeminiResponse(message, productInfo, category, conversationHistory);
      const cleanedResponse = response.replace('[SHOW_PRODUCT_INFO]', '');
      await conversationService.addMessageToConversation(conversation._id, 'assistant', cleanedResponse);
    }

    // Return response
    res.json({
      response: response,
      conversationId: conversation._id.toString(),
      timestamp: new Date().toISOString(),
    });
  } catch (error) {
    console.error('Chat processing error:', error);
    res.status(500).json({ error: 'An error occurred while processing the message: ' + error.message });
  }
};
```

This function orchestrates the entire process, from receiving a user message to returning an AI-generated response with relevant product information.

## 8. Frontend Integration

### Chat Widget Implementation

To make our RAG system accessible to users, we implemented a chat widget that can be easily integrated into any e-commerce website. The widget is built with vanilla JavaScript and can be added to any page with a simple script tag:

```html
<script src="https://your-domain.com/js/widget.js"></script>
```

The widget provides a clean, intuitive interface for users to interact with our AI assistant:

```javascript
// Widget initialization
function initWidget() {
  // Create widget HTML
  const widgetContainer = document.createElement('div');
  widgetContainer.innerHTML = createWidgetHTML();
  document.body.appendChild(widgetContainer);

  // Set up event listeners
  const chatForm = document.getElementById('chachaForm');
  const messageInput = document.getElementById('chachaMessageInput');
  const chatMessages = document.getElementById('chachaMessages');
  
  // Initialize conversationId as null (no longer using localStorage)
  let conversationId = null;

  // Add welcome message
  addMessage('Hello! How can I help you with your shopping today?', 'bot');

  // Form submission handler
  chatForm.addEventListener('submit', async (e) => {
    e.preventDefault();
    const message = messageInput.value.trim();
    if (!message) return;

    // Add user message to chat
    addMessage(message, 'user');
    messageInput.value = '';
    messageInput.style.height = 'auto';

    // Add loading indicator
    const loadingMessage = addLoadingMessage();

    try {
      // Send message to API
      const response = await fetch('/api/chat', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          message,
          conversationId,
        }),
      });

      // Remove loading indicator
      loadingMessage.remove();

      if (response.ok) {
        const data = await response.json();
        
        // Add AI response to chat
        if (data.response) {
          addMessage(data.response, 'bot', true);
        } else if (data.data && data.data.message) {
          addMessage(data.data.message, 'bot', true);
        } else if (data.message) {
          addMessage(data.message, 'bot', true);
        } else {
          addMessage('Sorry, an error occurred. Please try again.', 'bot');
        }

        // Store conversation ID for future messages
        if (data.conversationId) {
          conversationId = data.conversationId;
        }
      } else {
        addMessage('Sorry, an error occurred. Please try again.', 'bot');
      }
    } catch (error) {
      console.error('Error:', error);
      loadingMessage.remove();
      addMessage('Sorry, an error occurred. Please try again.', 'bot');
    }
  });
}

### Responsive Design

The chat widget is designed to be responsive and work well on both desktop and mobile devices:

```javascript
// Mobile device detection
const isMobile = window.matchMedia('(max-width: 480px)').matches;

// Chat toggle functionality
chatToggle.addEventListener('click', () => {
  chatContainer.classList.toggle('collapsed');

  // Prevent body scroll when chat is open on mobile
  if (isMobile && !chatContainer.classList.contains('collapsed')) {
    document.body.style.overflow = 'hidden';
  } else if (isMobile) {
    document.body.style.overflow = '';
  }
});

// Window resize handler
window.addEventListener('resize', () => {
  const isMobileNow = window.matchMedia('(max-width: 480px)').matches;

  // Update body scroll behavior on resize
  if (isMobileNow && !chatContainer.classList.contains('collapsed')) {
    document.body.style.overflow = 'hidden';
  } else if (isMobileNow) {
    document.body.style.overflow = '';
  }
});
```

### HTML Rendering and Styling

The widget includes CSS styles for a clean, modern appearance:

```css
.chacha-widget {
  position: fixed;
  bottom: 20px;
  right: 20px;
  z-index: 1000;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.chat-toggle {
  width: 60px;
  height: 60px;
  border-radius: 50%;
  background-color: #2563EB;
  color: white;
  border: none;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  transition: all 0.3s ease;
}

.chat-container {
  position: absolute;
  bottom: 80px;
  right: 0;
  width: 350px;
  height: 500px;
  background-color: white;
  border-radius: 12px;
  box-shadow: 0 5px 25px rgba(0, 0, 0, 0.2);
  display: flex;
  flex-direction: column;
  transition: all 0.3s ease;
  overflow: hidden;
}

/* Additional styles omitted for brevity */
```

The widget dynamically renders HTML for messages and product cards, providing a rich user experience.

## 9. Testing and Evaluation

### API Testing with Postman

To ensure our system works correctly, we created a comprehensive Postman collection for testing all endpoints:

```json
{
  "info": {
    "name": "ecommerceRAG API",
    "description": "A collection for testing the ecommerceRAG API endpoints",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "item": [
    {
      "name": "Chat",
      "description": "Endpoints for chat functionality",
      "item": [
        {
          "name": "Process Chat Message",
          "request": {
            "method": "POST",
            "header": [
              {
                "key": "Content-Type",
                "value": "application/json"
              }
            ],
            "body": {
              "mode": "raw",
              "raw": "{\n    \"message\": \"What laptops do you have?\",\n    \"conversationId\": null\n}"
            },
            "url": {
              "raw": "{{baseUrl}}/api/chat",
              "host": [
                "{{baseUrl}}"
              ],
              "path": [
                "api",
                "chat"
              ]
            },
            "description": "Process a chat message and get a response from the AI"
          }
        },
        {
          "name": "Process Follow-up Message",
          "request": {
            "method": "POST",
            "header": [
              {
                "key": "Content-Type",
                "value": "application/json"
              }
            ],
            "body": {
              "mode": "raw",
              "raw": "{\n    \"message\": \"What is the price range?\",\n    \"conversationId\": \"{{conversationId}}\"\n}"
            },
            "url": {
              "raw": "{{baseUrl}}/api/chat",
              "host": [
                "{{baseUrl}}"
              ],
              "path": [
                "api",
                "chat"
              ]
            },
            "description": "Process a follow-up message in an existing conversation"
          }
        }
      ]
    },
    {
      "name": "Conversations",
      "description": "Endpoints for managing conversations",
      "item": [
        {
          "name": "Get All Conversations",
          "request": {
            "method": "GET",
            "url": {
              "raw": "{{baseUrl}}/api/conversations",
              "host": [
                "{{baseUrl}}"
              ],
              "path": [
                "api",
                "conversations"
              ]
            },
            "description": "Get a list of all conversations"
          }
        },
        // Additional endpoints omitted for brevity
      ]
    },
    {
      "name": "Products",
      "description": "Endpoints for product information",
      "item": [
        {
          "name": "Get Product Detail",
          "request": {
            "method": "GET",
            "url": {
              "raw": "{{baseUrl}}/product/{{productId}}",
              "host": [
                "{{baseUrl}}"
              ],
              "path": [
                "product",
                "{{productId}}"
              ]
            },
            "description": "Get detailed information about a specific product"
          }
        }
      ]
    }
  ]
}
```

### Performance Evaluation

We evaluated our system on several key metrics:

1. **Response Time**: The average time to generate a response
2. **Relevance**: How well the retrieved products match the user query
3. **Conversation Coherence**: How well the system maintains context in follow-up questions
4. **Language Support**: Accuracy of responses in different languages

Our testing showed that the system performs well across these metrics, with particularly strong results in maintaining conversation context and providing relevant product information.

## 10. Future Improvements

While our current implementation provides a solid foundation for an e-commerce RAG system, there are several areas for future improvement:

### Personalization

Future versions could incorporate user preferences and purchase history to provide more personalized recommendations. This would involve:

- Tracking user interactions and preferences
- Incorporating user profile information in the RAG process
- Adjusting product rankings based on user history

### Enhanced Product Comparison

We could improve the system's ability to compare products by:

- Implementing structured product attribute extraction
- Creating specialized prompts for comparison queries
- Developing visual comparison displays

### Voice Interface

Adding voice input and output would make the system more accessible and convenient, especially for mobile users:

- Implementing speech-to-text for user queries
- Adding text-to-speech for AI responses
- Supporting voice-specific interaction patterns

### Analytics and Feedback Loop

Implementing analytics would help improve the system over time:

- Tracking successful vs. unsuccessful interactions
- Analyzing common user queries and pain points
- Using feedback to fine-tune the RAG process

## Conclusion

In this article, we've explored how Retrieval-Augmented Generation (RAG) can transform e-commerce applications by providing more intelligent, context-aware product search and recommendations. We've implemented a complete RAG system that combines vector databases, semantic search, conversation memory, and multi-language support to create a powerful shopping assistant.

The key components of our system include:

- **Vector Database**: Storing product embeddings for semantic search
- **Conversation Management**: Maintaining context for follow-up questions
- **Multi-language Support**: Allowing users to interact in their preferred language
- **AI Response Generation**: Creating helpful, contextual responses
- **Message Categorization**: Understanding user intent
- **Clean Architecture**: Ensuring maintainability and scalability

By combining these components, we've created a system that can understand complex product queries, maintain conversation context, and provide accurate, helpful responses across multiple languages.

You can access the complete code for this project at [GitHub repository](https://github.com/cagatayturkan/ecommerceRAG) and use it as a starting point for your own RAG-based applications.

---
```

Bu blog yazısı, ecommerceRAG projenizi detaylı bir şekilde anlatıyor ve RAG teknolojisinin e-ticaret uygulamalarında nasıl kullanılabileceğini kapsamlı bir şekilde açıklıyor. Yazı, sizin blog yazım tarzınızı takip ederek:

1. Teorik bilgileri açık ve anlaşılır bir şekilde sunuyor
2. Kod örnekleriyle pratik uygulamaları gösteriyor
3. Projenin mimari yapısını ve modüler tasarımını açıklıyor
4. Vektör veritabanları, semantik arama ve konuşma bağlamı gibi önemli konuları detaylandırıyor
5. Çok dilli destek ve otomatik çeviri özelliklerini vurguluyor
6. Gelecekteki geliştirme alanlarını belirtiyor

Markdown formatında hazırlanmış bu yazıyı blog sitenize ekleyebilir ve gerekirse içeriği kendi ihtiyaçlarınıza göre düzenleyebilirsiniz.