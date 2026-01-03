# Shopify Liquid Learning Notes (Claude AI - Explanatory)

## Cart Section

### Cart Item

Working with cart items requires understanding various properties for displaying product details, pricing, and discounts. Each cart item contains essential data about the product, its variants, quantities, and any applied discounts.

#### Cart Item Details

These properties help you display product information and manage cart interactions:

- **`item.product`** - The product object containing all product data
- **`item.title`** - The product title
- **`item.product.handle`** - URL handle for linking to the product page
- **`item.variant.title`** - The variant title (size, color, etc.)
- **`item.variant.id`** - Unique variant identifier
- **`item.quantity`** - Number of items in cart
- **`item.image`** - Product image object
- **`item.url`** - Direct URL to the product variant
- **`item.key`** - Unique line item key for cart updates
- **`item.properties`** - Custom line item properties (like personalization)
- **`item.selling_plan_allocation`** - Subscription information if applicable
- **`item.vendor`** - Product vendor/brand name
- **`item.sku`** - Stock keeping unit identifier

#### Cart Item Pricing

Understanding the three core pricing properties and when to use each:

- **`item.original_price`** - Price per single unit before discounts
- **`item.final_price`** - Price per single unit after discounts
- **`item.original_line_price`** - Total price (quantity × original price)
- **`item.final_line_price`** - Total price after all discounts (quantity × final price)
- **`item.line_price`** - Same as final_line_price (deprecated but still works)
- **`item.variant.price`** - Base variant price
- **`item.variant.compare_at_price`** - Original "was" price for sales

#### Cart Item Discounts

Properties for displaying and calculating discounts:

- **`item.line_level_discount_allocations`** - Array of discounts applied to this line item
- **`item.discount_allocations`** - Same as above (alternate naming)
- **`discount_allocation.discount_application.title`** - Discount code or automatic discount name
- **`discount_allocation.amount`** - Amount saved from this specific discount
- **`item.line_level_total_discount`** - Total discount amount for this line item
- **`item.message`** - Optional gift message or note

#### Complete Cart Item Code Example

Here's a comprehensive cart item component with all essentials:

```liquid
{% for item in cart.items %}
  <div class="cart-item" data-cart-item data-item-key="{{ item.key }}">
    
    <!-- Product Image -->
    <div class="cart-item__image">
      <a href="{{ item.url }}">
        {% if item.image %}
          <!-- Result: <img src="https://cdn.shopify.com/products/blue-shirt_200x200.jpg" alt="Blue Cotton T-Shirt"> -->
          <img src="{{ item.image | img_url: '200x200' }}" 
               alt="{{ item.image.alt | escape }}"
               loading="lazy">
        {% else %}
          <!-- Result: Default placeholder SVG for products without images -->
          {{ 'product-1' | placeholder_svg_tag }}
        {% endif %}
      </a>
    </div>
    
    <!-- Product Details -->
    <div class="cart-item__details">
      <h3 class="cart-item__title">
        <!-- Result: "Premium Cotton T-Shirt" as clickable link -->
        <a href="{{ item.url }}">{{ item.product.title }}</a>
      </h3>
      
      {% if item.product.vendor %}
        <!-- Result: "Acme Clothing Co." or empty if no vendor -->
        <p class="cart-item__vendor">{{ item.product.vendor }}</p>
      {% endif %}
      
      {% unless item.product.has_only_default_variant %}
        <!-- Result: "Large / Blue" or "Size: XL, Color: Red" -->
        <p class="cart-item__variant">{{ item.variant.title }}</p>
      {% endunless %}
      
      {% if item.sku %}
        <!-- Result: "SKU: BLU-SHIRT-LG" or empty if no SKU -->
        <p class="cart-item__sku">SKU: {{ item.sku }}</p>
      {% endif %}
      
      <!-- Custom Properties (like personalization) -->
      {% if item.properties.size > 0 %}
        <dl class="cart-item__properties">
          {% for property in item.properties %}
            {% unless property.last == blank %}
              <!-- Result: "Personalization:" followed by "John Smith" -->
              <dt>{{ property.first }}:</dt>
              <dd>
                {% if property.last contains '/uploads/' %}
                  <!-- Result: <a href="/uploads/custom-logo.png" target="_blank">View File</a> -->
                  <a href="{{ property.last }}" target="_blank">View File</a>
                {% else %}
                  <!-- Result: "John Smith" or "Please gift wrap" -->
                  {{ property.last }}
                {% endif %}
              </dd>
            {% endunless %}
          {% endfor %}
        </dl>
      {% endif %}
      
      <!-- Selling Plan (Subscriptions) -->
      {% if item.selling_plan_allocation %}
        <!-- Result: "Deliver every 30 days" or "Subscribe & Save 20%" -->
        <p class="cart-item__selling-plan">
          {{ item.selling_plan_allocation.selling_plan.name }}
        </p>
      {% endif %}
    </div>
    
    <!-- Pricing Display -->
    <div class="cart-item__pricing">
      <!-- Unit Price -->
      <div class="cart-item__price-per-item">
        {% if item.original_price != item.final_price %}
          <!-- Result: <s>$25.00</s> for original strikethrough price -->
          <s class="cart-item__original-price">
            {{ item.original_price | money }}
          </s>
          <!-- Result: "$20.00" for discounted price -->
          <span class="cart-item__final-price">
            {{ item.final_price | money }}
          </span>
          <span class="cart-item__discount-badge">
            {% assign discount_percentage = item.original_price | minus: item.final_price | times: 100.0 | divided_by: item.original_price | round %}
            <!-- Result: "-20%" discount badge -->
            -{{ discount_percentage }}%
          </span>
        {% else %}
          <!-- Result: "$25.00" for regular price with no discount -->
          <span class="cart-item__regular-price">
            {{ item.original_price | money }}
          </span>
        {% endif %}
      </div>
      
      <!-- Line Item Discounts -->
      {% if item.line_level_discount_allocations.size > 0 %}
        <ul class="cart-item__discounts">
          {% for discount_allocation in item.line_level_discount_allocations %}
            <li class="cart-item__discount">
              <svg class="icon icon-discount" width="12" height="12">
                <use xlink:href="#icon-discount"></use>
              </svg>
              <!-- Result: "SUMMER20" or "Buy 2 Get 1 Free" -->
              <span>{{ discount_allocation.discount_application.title }}</span>
              <!-- Result: "(-$5.00)" showing amount saved -->
              <span>(-{{ discount_allocation.amount | money }})</span>
            </li>
          {% endfor %}
        </ul>
      {% endif %}
    </div>
    
    <!-- Quantity Selector -->
    <div class="cart-item__quantity">
      <!-- Result: "Quantity for Premium Cotton T-Shirt" (hidden label) -->
      <label for="quantity-{{ item.key }}" class="visually-hidden">
        Quantity for {{ item.product.title }}
      </label>
      <quantity-input class="quantity">
        <button class="quantity__button" type="button" name="minus" aria-label="Decrease quantity">
          <svg width="12" height="2"><rect width="12" height="2"/></svg>
        </button>
        <!-- Result: <input value="2"> showing current quantity -->
        <input type="number" 
               class="quantity__input"
               id="quantity-{{ item.key }}"
               min="0"
               value="{{ item.quantity }}"
               data-item-key="{{ item.key }}"
               aria-label="Quantity">
        <button class="quantity__button" type="button" name="plus" aria-label="Increase quantity">
          <svg width="12" height="12"><rect x="5" width="2" height="12"/><rect y="5" width="12" height="2"/></svg>
        </button>
      </quantity-input>
    </div>
    
    <!-- Line Total -->
    <div class="cart-item__total">
      {% if item.original_line_price != item.final_line_price %}
        <!-- Result: <s>$50.00</s> for original total (2 × $25.00) -->
        <s class="cart-item__original-total">
          {{ item.original_line_price | money }}
        </s>
        <!-- Result: "$40.00" for discounted total (2 × $20.00) -->
        <span class="cart-item__final-total">
          {{ item.final_line_price | money }}
        </span>
        <span class="cart-item__savings">
          {% assign savings = item.original_line_price | minus: item.final_line_price %}
          <!-- Result: "Save $10.00" showing total line savings -->
          Save {{ savings | money }}
        </span>
      {% else %}
        <!-- Result: "$50.00" for regular total with no discount -->
        <span class="cart-item__regular-total">
          {{ item.final_line_price | money }}
        </span>
      {% endif %}
    </div>
    
    <!-- Remove Button -->
    <div class="cart-item__remove">
      <!-- Result: "Remove Premium Cotton T-Shirt" (aria-label) -->
      <button type="button" 
              class="cart-item__remove-button"
              data-item-key="{{ item.key }}"
              aria-label="Remove {{ item.product.title }}">
        <svg width="14" height="14">
          <path d="M1 1l12 12M13 1L1 13" stroke="currentColor" stroke-width="1.5"/>
        </svg>
      </button>
    </div>
    
  </div>
{% endfor %}
```

### Cart Summary

The cart summary displays totals, applied discounts, and checkout information. Understanding these properties helps create accurate order summaries.

#### Cart Summary Pricing

Essential properties for calculating and displaying cart totals:

- **`cart.total_price`** - Final total after all discounts
- **`cart.original_total_price`** - Total before any discounts
- **`cart.total_discount`** - Total amount saved from all discounts
- **`cart.item_count`** - Total number of items in cart
- **`cart.items.size`** - Number of unique line items
- **`cart.total_weight`** - Combined weight of all items
- **`cart.currency.iso_code`** - Currency code (USD, EUR, etc.)
- **`cart.currency.symbol`** - Currency symbol ($, €, etc.)
- **`cart.taxes_included`** - Boolean if taxes are included in prices
- **`cart.duties_included`** - Boolean if duties are included

#### Cart Summary Discounts

Properties for displaying cart-level and total discounts:

- **`cart.cart_level_discount_applications`** - Array of cart-wide discounts
- **`cart.discount_applications`** - All discount applications (line + cart level)
- **`discount_application.title`** - Discount name/code
- **`discount_application.total_allocated_amount`** - Total discount amount
- **`discount_application.type`** - Type of discount (automatic, discount_code, script, manual)
- **`cart.attributes`** - Custom cart attributes (like gift notes)

#### Complete Cart Summary Code Example

Here's a comprehensive cart summary component with all essentials:

```liquid
<div class="cart-summary">
  
  <!-- Cart Attributes (Gift Message, Special Instructions) -->
  <div class="cart-summary__notes">
    <label for="cart-note">Order Special Instructions</label>
    <!-- Result: "Please leave package at the front door" or empty -->
    <textarea name="note" id="cart-note" placeholder="How can we help?">
      {{ cart.note }}
    </textarea>
  </div>
  
  <!-- Subtotal Section -->
  <div class="cart-summary__subtotal">
    <div class="cart-summary__row">
      <!-- Result: "Subtotal (5 items)" or "Subtotal (1 item)" -->
      <span>Subtotal ({{ cart.item_count }} 
        {%- if cart.item_count == 1 %} item{% else %} items{% endif -%})</span>
      <!-- Result: "$125.00" (original price before any discounts) -->
      <span>{{ cart.original_total_price | money }}</span>
    </div>
  </div>
  
  <!-- Discounts Section -->
  {% if cart.cart_level_discount_applications.size > 0 or cart.total_discount > 0 %}
    <div class="cart-summary__discounts">
      
      <!-- Cart Level Discounts -->
      {% for discount_application in cart.cart_level_discount_applications %}
        <div class="cart-summary__row cart-summary__discount">
          <span class="cart-summary__discount-title">
            <svg class="icon icon-discount" width="12" height="12">
              <use xlink:href="#icon-discount"></use>
            </svg>
            <!-- Result: "SUMMER20" or "Free Shipping on $50+" -->
            {{ discount_application.title }}
            {% case discount_application.type %}
              {% when 'automatic' %}
                <!-- Result: "AUTO" badge for automatic discounts -->
                <span class="badge">AUTO</span>
              {% when 'discount_code' %}
                <!-- Result: "CODE" badge for discount codes -->
                <span class="badge">CODE</span>
              {% when 'script' %}
                <!-- Result: "CUSTOM" badge for script discounts -->
                <span class="badge">CUSTOM</span>
            {% endcase %}
          </span>
          <!-- Result: "-$25.00" (amount saved from this discount) -->
          <span class="cart-summary__discount-amount">
            -{{ discount_application.total_allocated_amount | money }}
          </span>
        </div>
      {% endfor %}
      
      <!-- Total Discount Summary -->
      {% if cart.total_discount > 0 %}
        <div class="cart-summary__row cart-summary__total-discount">
          <span>Total Savings</span>
          <!-- Result: "-$31.25" (total of all discounts) -->
          <span class="cart-summary__savings-amount">
            -{{ cart.total_discount | money }}
          </span>
        </div>
      {% endif %}
    </div>
  {% endif %}
  
  <!-- Discount Code Input -->
  <div class="cart-summary__discount-form">
    <form action="/discount" method="post">
      <div class="field">
        <label for="discount-code" class="visually-hidden">Discount code</label>
        <div class="field__input-wrapper">
          <input type="text" 
                 id="discount-code" 
                 name="discount_code" 
                 placeholder="Discount code"
                 autocomplete="off">
          <button type="submit" class="button button--secondary">Apply</button>
        </div>
      </div>
    </form>
  </div>
  
  <!-- Shipping Calculator (Optional) -->
  <div class="cart-summary__shipping">
    <div class="cart-summary__row">
      <span>Shipping</span>
      <!-- Result: "Calculated at checkout" or "$5.99" if rates available -->
      <span>Calculated at checkout</span>
    </div>
    {% if cart.total_weight > 0 %}
      <!-- Result: "Total weight: 2.5 kg" or "Total weight: 5.5 lbs" -->
      <p class="cart-summary__weight">
        Total weight: {{ cart.total_weight | weight_with_unit }}
      </p>
    {% endif %}
  </div>
  
  <!-- Tax Information -->
  <div class="cart-summary__taxes">
    <div class="cart-summary__row">
      <span>
        Taxes
        {% if cart.taxes_included %}
          <!-- Result: "(included)" text if taxes are in the price -->
          <span class="cart-summary__tax-note">(included)</span>
        {% endif %}
      </span>
      <span>
        {% if cart.taxes_included %}
          <!-- Result: "Included" -->
          Included
        {% else %}
          <!-- Result: "Calculated at checkout" -->
          Calculated at checkout
        {% endif %}
      </span>
    </div>
    
    {% if cart.duties_included %}
      <div class="cart-summary__row">
        <span>Duties (included)</span>
        <!-- Result: "Included" -->
        <span>Included</span>
      </div>
    {% endif %}
  </div>
  
  <!-- Total Section -->
  <div class="cart-summary__total">
    <div class="cart-summary__row cart-summary__row--total">
      <span class="cart-summary__total-label">Total</span>
      <span class="cart-summary__total-price">
        <!-- Result: "USD" or "EUR" or "GBP" -->
        <span class="cart-summary__currency">{{ cart.currency.iso_code }}</span>
        <!-- Result: "$93.75 USD" (final price after all discounts) -->
        {{ cart.total_price | money_with_currency }}
      </span>
    </div>
    
    <!-- Show savings if there are discounts -->
    {% if cart.total_discount > 0 %}
      <div class="cart-summary__row cart-summary__row--savings">
        <!-- Result: "You're saving $31.25!" -->
        <span class="cart-summary__savings-text">
          You're saving {{ cart.total_discount | money }}!
        </span>
      </div>
    {% endif %}
  </div>
  
  <!-- Payment Icons -->
  <div class="cart-summary__payment-icons">
    {% for type in shop.enabled_payment_types %}
      <!-- Result: Visa, Mastercard, PayPal, Apple Pay icons -->
      {{ type | payment_type_svg_tag: class: 'payment-icon' }}
    {% endfor %}
  </div>
  
  <!-- Checkout Buttons -->
  <div class="cart-summary__buttons">
    <button type="submit" 
            name="update" 
            class="button button--secondary"
            form="cart-form">
      Update Cart
    </button>
    
    <!-- Result: "Checkout • $93.75 USD" or disabled if cart empty -->
    <button type="submit" 
            name="checkout" 
            class="button button--primary"
            form="cart-form"
            {% if cart.item_count == 0 %}disabled{% endif %}>
      Checkout • {{ cart.total_price | money_with_currency }}
    </button>
    
    <!-- Dynamic Checkout Buttons (PayPal, Apple Pay, etc.) -->
    {% if additional_checkout_buttons %}
      <div class="cart-summary__additional-checkout">
        <!-- Result: PayPal, Apple Pay, Google Pay buttons -->
        {{ content_for_additional_checkout_buttons }}
      </div>
    {% endif %}
  </div>
  
  <!-- Free Shipping Progress Bar (Optional) -->
  {% assign free_shipping_threshold = 10000 %}
  <!-- Result: free_shipping_threshold = $100.00 in cents -->
  {% assign free_shipping_remaining = free_shipping_threshold | minus: cart.total_price %}
  
  {% if free_shipping_remaining > 0 %}
    <div class="cart-summary__free-shipping">
      <!-- Result: "Add $6.25 more for free shipping!" -->
      <p>Add {{ free_shipping_remaining | money }} more for free shipping!</p>
      <div class="progress-bar">
        <!-- Result: Progress bar filled to 93.75% if cart is $93.75 and threshold is $100 -->
        {% assign progress_percentage = cart.total_price | times: 100.0 | divided_by: free_shipping_threshold %}
        <div class="progress-bar__fill" style="width: {{ progress_percentage }}%"></div>
      </div>
    </div>
  {% else %}
    <div class="cart-summary__free-shipping cart-summary__free-shipping--complete">
      <!-- Result: "✓ You've qualified for free shipping!" -->
      <p>✓ You've qualified for free shipping!</p>
    </div>
  {% endif %}
  
</div>
```

---

## Block Targeting and Rendering

### Understanding the Three Ways to Render Blocks

When building Shopify themes, you have three distinct approaches for rendering blocks that work both in sections and standalone blocks liquid files. Each method serves different purposes and gives you varying levels of control over where and how blocks appear. Think of these as different "targeting systems" for your content blocks.

**Important Note**: These techniques aren't limited to sections - they work equally well in blocks liquid files located in your theme's `/blocks/` directory. This architectural flexibility allows you to create reusable, modular components that can be referenced from multiple sections or even other blocks.

#### 1. Static Block Targeting - Precise Control with Variable Passing

Static block targeting allows you to render a specific block in an exact location, regardless of the order blocks appear in the theme editor. This approach becomes incredibly powerful when you realize you can pass variables and settings directly into the block, creating dynamic yet controlled rendering scenarios.

**Method 1: Direct ID/Type Targeting with Variable Passing**
```liquid
<!-- Basic static block targeting -->
{% content_for "block", type: "hero_banner", id: "hero-main" %}

<!-- Advanced: Passing section settings to customize the block -->
{% content_for "block", type: "my-static-block", id: "unique-id", bg_color: section.settings.bg_color %}

<!-- Example: Custom announcement bar with section-level styling -->
<div class="announcement-wrapper">
  {% content_for "block", 
      type: "announcement", 
      id: "main-announcement", 
      text_color: section.settings.announcement_text_color,
      background: section.settings.announcement_bg,
      font_size: section.settings.announcement_size %}
</div>

<!-- Example: Product showcase with dynamic pricing display -->
<div class="featured-product">
  {% content_for "block", 
      type: "product_card", 
      id: "featured-product", 
      product_handle: section.settings.featured_product,
      show_price: section.settings.show_pricing,
      currency_symbol: shop.money_format %}
</div>
```

The real power here lies in understanding that you can pass any liquid variable, setting, or computed value into your static blocks. This creates a bridge between your section's configuration and reusable block components, allowing you to maintain design consistency while enabling customization at the section level.

**Method 2: Conditional Loop Filtering (Section-Specific Rendering)**
```liquid
<!-- This loops through all blocks but only renders specific types -->
{% for block in section.blocks %}
  {% if block.type == 'hero_banner' %}
    <div class="hero-container" {{ block.shopify_attributes }}>
      <h1>{{ block.settings.headline }}</h1>
      <p>{{ block.settings.description }}</p>
    </div>
  {% endif %}
{% endfor %}

<!-- Example: Only show testimonial blocks in a specific area -->
<div class="testimonials-section">
  {% for block in section.blocks %}
    {% if block.type == 'testimonial' %}
      <div class="testimonial-card" {{ block.shopify_attributes }}>
        <blockquote>{{ block.settings.quote }}</blockquote>
        <cite>{{ block.settings.author }}</cite>
      </div>
    {% endif %}
  {% endfor %}
</div>
```

**Understanding Variable Passing in Static Blocks**

When you pass variables to static blocks using the `content_for` approach, you're essentially creating a communication channel between your section and standalone block files. This technique works because Shopify allows you to define custom parameters that get passed directly to the block's liquid context.

Consider this practical example where you want to create a reusable testimonial block that adapts to different section styles:

```liquid
<!-- In your section file: sections/testimonials.liquid -->
<div class="testimonials-container">
  {% content_for "block", 
      type: "testimonial_card", 
      id: "testimonial-1", 
      theme_style: section.settings.card_style,
      text_alignment: section.settings.text_align,
      show_rating: section.settings.display_stars,
      custom_bg: section.settings.card_background %}
</div>

<!-- In your block file: blocks/testimonial_card.liquid -->
<div class="testimonial-card testimonial-card--{{ theme_style }}" 
     style="background-color: {{ custom_bg }}; text-align: {{ text_alignment }};">
  
  {% if show_rating and block.settings.rating %}
    <div class="rating">
      {% for i in (1..block.settings.rating) %}
        <span class="star">★</span>
      {% endfor %}
    </div>
  {% endif %}
  
  <blockquote>{{ block.settings.quote }}</blockquote>
  <cite>{{ block.settings.author }}</cite>
</div>
```

This approach creates incredibly flexible components. The same testimonial block can appear completely different depending on which section calls it and what variables are passed to it, yet you maintain the core testimonial logic in a single, reusable file.

#### Working with Blocks Liquid Files

An essential concept to grasp is that these block targeting techniques extend far beyond section files. When you create standalone block files in your theme's `/blocks/` directory, you're building reusable components that can be called from anywhere in your theme architecture. This modular approach transforms how you think about theme development, shifting from section-specific code to component-based architecture.

Here's how this works in practice. When you create a file like `blocks/custom-hero.liquid`, that block becomes available throughout your entire theme. You can call it from multiple sections, pass different variables to customize its appearance, and maintain consistency across your theme while avoiding code duplication.

```liquid
<!-- Example: Calling the same block from different sections with different styling -->

<!-- From sections/homepage.liquid -->
{% content_for "block", 
    type: "custom-hero", 
    id: "homepage-hero", 
    style: "full-width",
    overlay_opacity: 0.7,
    text_position: "center" %}

<!-- From sections/collection.liquid -->
{% content_for "block", 
    type: "custom-hero", 
    id: "collection-hero", 
    style: "contained",
    overlay_opacity: 0.5,
    text_position: "left",
    collection: collection %}

<!-- From sections/page.liquid -->
{% content_for "block", 
    type: "custom-hero", 
    id: "page-hero", 
    style: "minimal",
    show_breadcrumbs: true,
    page_title: page.title %}
```

In your `blocks/custom-hero.liquid` file, you would then handle these passed variables to create different presentations of the same core component. This approach creates incredible flexibility while maintaining code organization and reducing maintenance overhead.

**Understanding the Key Difference Between Conditional Loop Filtering and Dynamic Rendering:**

The second method creates an interesting scenario that reveals the sophisticated architecture of Shopify's block system. When you use the conditional loop approach, you're essentially creating a content filter for that specific area. The crucial insight here is that this creates a separation between what merchants can add versus what actually displays in any given location.

Here's a practical example that demonstrates this concept clearly:

```liquid
<div class="header-section">
  <!-- This area will ONLY show logo blocks, even though merchants can add other block types -->
  {% for block in section.blocks %}
    {% if block.type == 'logo' %}
      <img src="{{ block.settings.logo_image | img_url: '200x' }}" alt="Logo">
    {% endif %}
  {% endfor %}
</div>

<div class="content-section">
  <!-- This area will show ALL blocks that were added -->
  {% content_for 'blocks' %}
</div>
```

Think of this like having a restaurant with different dining areas. Merchants can order any item from the menu, but certain dishes will only appear in specific dining rooms based on your architectural decisions. In the header section, only logo blocks will be served, regardless of what other blocks merchants have added. However, in the content section, everything on the order gets delivered.

This architectural pattern gives you tremendous control over layout and user experience while maintaining merchant flexibility. Merchants can add logo blocks, text blocks, image blocks, and any other block type you've defined in your schema. The header section will only display logo blocks, while the content section will display everything. This approach allows you to create structured, predictable layouts while still empowering merchants with creative control in appropriate areas.

#### 2. Dynamic Block Rendering - Sequential Display

Dynamic block rendering displays all blocks in the order they appear in the theme editor. This is like a queue system where blocks are processed in the sequence merchants arrange them.

```liquid
<!-- This renders ALL blocks in the order set by the merchant -->
{% content_for 'blocks' %}

<!-- Example: Main content area where merchants control the flow -->
<div class="page-content">
  {% content_for 'blocks' %}
</div>

<!-- Example: Flexible homepage sections -->
<main class="homepage">
  {% content_for 'blocks' %}
</main>
```

When you use `{% content_for 'blocks' %}`, Shopify automatically renders each block according to its type, using the HTML structure you've defined in your conditional statements later in the template. The key advantage here is that merchants have complete control over the ordering and can rearrange blocks through the theme editor's drag-and-drop interface.

#### 3. Theme Block Targeting - Schema-Driven Control

Theme block targeting happens through your section's schema configuration. This method defines what block types are available, their settings, and their default behavior. Think of this as creating the "blueprint" for how blocks can be used.

```liquid
<!-- In your section file, the schema defines available blocks -->
{% schema %}
{
  "name": "Flexible Content Section",
  "blocks": [
    {
      "type": "text_block",
      "name": "Text Block",
      "settings": [
        {
          "type": "textarea",
          "id": "content",
          "label": "Text Content",
          "default": "Enter your text here..."
        }
      ]
    },
    {
      "type": "image_block",
      "name": "Image Block",
      "settings": [
        {
          "type": "image_picker",
          "id": "image",
          "label": "Choose Image"
        },
        {
          "type": "text",
          "id": "alt_text",
          "label": "Alt Text"
        }
      ]
    },
    {
      "type": "button_block",
      "name": "Button Block",
      "settings": [
        {
          "type": "text",
          "id": "button_text",
          "label": "Button Text",
          "default": "Click Here"
        },
        {
          "type": "url",
          "id": "button_link",
          "label": "Button Link"
        }
      ]
    }
  ],
  "presets": [
    {
      "name": "Content Section",
      "blocks": [
        {
          "type": "text_block"
        },
        {
          "type": "image_block"
        }
      ]
    }
  ]
}
{% endschema %}

<!-- Then in your liquid code, handle each block type -->
{% for block in section.blocks %}
  {% case block.type %}
    {% when 'text_block' %}
      <div class="text-content" {{ block.shopify_attributes }}>
        {{ block.settings.content | newline_to_br }}
      </div>
    
    {% when 'image_block' %}
      <div class="image-content" {{ block.shopify_attributes }}>
        {% if block.settings.image %}
          <img src="{{ block.settings.image | img_url: '800x' }}" 
               alt="{{ block.settings.alt_text | escape }}">
        {% endif %}
      </div>
    
    {% when 'button_block' %}
      <div class="button-content" {{ block.shopify_attributes }}>
        {% if block.settings.button_link %}
          <a href="{{ block.settings.button_link }}" class="btn">
            {{ block.settings.button_text }}
          </a>
        {% endif %}
      </div>
  {% endcase %}
{% endfor %}
```

#### Understanding the Architectural Differences

The power of these three approaches becomes clear when you understand how they work together to create flexible yet controlled layouts. Consider this practical example:

```liquid
<!-- Hero area: Static targeting for consistent branding -->
<div class="hero-banner">
  {% content_for "block", type: "hero", id: "main-hero" %}
</div>

<!-- Feature highlights: Filtered targeting for specific content types -->
<div class="features-row">
  {% for block in section.blocks %}
    {% if block.type == 'feature_highlight' %}
      <div class="feature-card" {{ block.shopify_attributes }}>
        <h3>{{ block.settings.title }}</h3>
        <p>{{ block.settings.description }}</p>
      </div>
    {% endif %}
  {% endfor %}
</div>

<!-- Main content: Dynamic rendering for merchant flexibility -->
<div class="main-content">
  {% content_for 'blocks' %}
</div>

<!-- Call-to-action: Static targeting for conversion optimization -->
<div class="cta-section">
  {% content_for "block", type: "cta", id: "primary-cta" %}
</div>
```

In this structure, you're giving merchants flexibility in the main content area while maintaining control over critical conversion elements like the hero banner and call-to-action sections. The features row will only show feature highlight blocks, but merchants can still add other block types that will appear in the main content area.

#### Best Practices for Block Targeting

When deciding which approach to use, consider the merchant's needs versus your design requirements. Static targeting works best for elements that must appear in specific locations for branding or conversion purposes. Dynamic rendering excels when you want to give merchants complete creative control. The conditional loop approach provides a middle ground, allowing architectural control while maintaining flexibility.

Remember to always include `{{ block.shopify_attributes }}` in your block markup when using loops. This ensures that theme editor functionality like drag-and-drop reordering and block-specific styling works correctly. The schema-driven approach requires careful planning of your block types and their settings to create an intuitive editing experience for merchants.

---

*Continue adding sections below as you learn more Shopify Liquid concepts...*
