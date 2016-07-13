---
layout: post
title: "Comments vs Self Documenting"
date: 2016-07-13 12:00:00 +0100
categories: [coding]
---

Consider some types.

    {% highlight go %}
    type Product struct {
    	SKU   string // Unique identifier of this type of product
    	Name  string // Friendly name
    	Price int    // Price in pence
    }

    type Line struct {
    	Name  string // Friendly name, copied from Product
    	Price int    // Price in pence
    	Tax   int    // Tax in pence
    }

    type Receipt struct {
    	Lines []Line // Collection of line items
    	Total int    // Total price in pence
    	Tax   int    // Total tax in pence
    }
    {% endhighlight %}

And consider a function that generates a receipt of line items from an array of products.

    {% highlight go %}
    func GetReceipt(products []Product, taxRate int) Receipt {
    	receipt := Receipt{make([]Line, 0), 0, 0}

    	for _, p := range products {
    		tax := int(p.Price * taxRate / 100)
    		line := Line{p.Name, p.Price, tax}
    		receipt.Lines = append(receipt.Lines, line)
    		receipt.Total += p.Price
    		receipt.Tax += tax
    	}
    	return receipt
    }
    {% endhighlight %}

Pretty simple. Not much going on there. It's quite obvious that the function
loops over the products, calculates the tax, creates a line item and adds it
to the receipt. Not to difficult.

But business requirements happen. It turns into this.

    {% highlight go %}
    func GetReceipt(products []Product, taxRate int) Receipt {
    	receipt := Receipt{make([]Line, 0), 0, 0}

    	for _, p := range products {
    		if strings.HasPrefix(p.SKU, "T") {
    			continue
    		}
    		tax := int(p.Price * taxRate / 100)
    		if strings.HasPrefix(p.SKU, "B") {
    			tax = 0
    		}
    		line := Line{p.Name, p.Price, tax}
    		receipt.Lines = append(receipt.Lines, line)
    		receipt.Total += p.Price
    		receipt.Tax += tax
    	}
    	return receipt
    }
    {% endhighlight %}

Still not the worst code ever, but some bits of it are a bit opaque. It continues
if the SKU starts with `T`. It sets tax to zero if the SKU starts with `B`.
None of this makes sense. The reviewer looking at the changes sends it back.

> Add some comments!

And so comments are added.

    {% highlight go %}
    func GetReceipt(products []Product, taxRate int) Receipt {
    	receipt := Receipt{make([]Line, 0), 0, 0}

    	for _, p := range products {
    		// Don't add if SKU starts with T
    		if strings.HasPrefix(p.SKU, "T") {
    			continue
    		}

    		// Only tax if SKU doesn't start with B
    		tax := 0
    		if !strings.HasPrefix(p.SKU, "B") {
    			tax = int(p.Price * taxRate / 100)
    		}

    		// Add the line item to the receipt
    		line := Line{p.Name, p.Price, tax}
    		receipt.Lines = append(receipt.Lines, line)

    		// Update running totals
    		receipt.Total += p.Price
    		receipt.Tax += tax
    	}

    	return receipt
    }
    {% endhighlight %}

Not exactly helpful. I can see from the code that the product is not added if
the SKU starts with `T`, and I can see from the code that the tax is zero if
the SKU starts with `B`. But I can't see *why* that is the case. What does `T`
mean? What does `B` mean? What does any of this mean?

    {% highlight go %}
    func GetReceipt(products []Product, taxRate int) Receipt {
    	receipt := Receipt{make([]Line, 0), 0, 0}

    	for _, p := range products {
    		// SKU starting with T are internal property and should not be sold
    		if strings.HasPrefix(p.SKU, "T") {
    			continue
    		}

    		// SKU starting with B are books and are not taxed
    		tax := 0
    		if !strings.HasPrefix(p.SKU, "B") {
    			tax = int(p.Price * taxRate / 100)
    		}

    		line := Line{p.Name, p.Price, tax}
    		receipt.Lines = append(receipt.Lines, line)

    		receipt.Total += p.Price
    		receipt.Tax += tax
    	}

    	return receipt
    }
    {% endhighlight %}

Well, that's better. Now I can clearly see that internal property is not for
sale, and that books are not taxed. Unfortunately it is a known law of the
universe that as soon as a comment is added to code to explain it, the code
changes and renders the comment obsolete.

The trick is of course to make the code self-documenting. But how?

How about this:

    {% highlight go %}
    // IsCompanyProperty
    func (product *Product) IsCompanyProperty() bool {
    	return strings.HasPrefix(product.SKU, "T")
    }

    // IsBook
    func (product *Product) IsBook() bool {
    	return strings.HasPrefix(product.SKU, "B")
    }

    // IsForSale
    func (product *Product) IsForSale() bool {
    	if product.IsCompanyProperty() {
    		return false
    	}
    	return true
    }

    // CalculateTax calculates tax at the specified rate if applicable, or returns 0
    func (product *Product) CalculateTax(taxRate int) int {
    	if !product.IsForSale() {
    		return 0
    	}

    	if product.IsBook() {
    		return 0
    	}

    	return int(product.Price * taxRate / 100)
    }

    // AddLine adds a single line item to the receipt and updates running totals
    func (receipt *Receipt) AddLine(line Line) {
    	receipt.Lines = append(receipt.Lines, line)

    	receipt.Total += line.Price
    	receipt.Tax += line.Tax
    }

    // GetReceipt creates a receipt with tax for the list of products given
    func GetReceipt(products []Product, taxRate int) (receipt Receipt) {
    	receipt = Receipt{make([]Line, 0), 0, 0}

    	for _, product := range products {
    		if product.IsForSale() {
    			line := Line{
    				Name:  product.Name,
    				Price: product.Price,
    				Tax:   product.CalculateTax(taxRate),
    			}

    			receipt.AddLine(line)
    		}
    	}
    	return
    }
    {% endhighlight %}

A bit more wordy, isn't it? But that's not a bad thing. Folk often lambast
Java for its verbosity, but it is pointless verbosity. Stu..Stu..Stuttering
over repeated class and variable names, multiple steps required to achieve
simple tasks. Horrifically forced nounification of verbs.

This is self-documenting in a better way. You can see clearly that a receipt
is made up of line items that have a name, a price and some tax. You can see
that the decision on how to apply tax is deferred to a specialised single
purpose function. You can see that only items for sale are added to the
receipt, and that the predicate for that condition is again deferred elsewhere.

And, as a bonus, it's possible to find out if a product is a book, or is for sale,
with the same rules everywhere in the program.

I'm sure this is teaching grandma to suck eggs, but I see it time and again
when coders add comments to explain something by describing the code in English.
Ronseal is great for painting fences, but keep it out of the codebase.
