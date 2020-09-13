---
layout: post
title: "Entity Framework Relationships !"
date: 2020-04-10 10:22:41 +0500
categories: ef efcore sql foreignkey
---

# Creating Associative Relationship

Setting column type

```cs
    builder.Property(order => order.CurrencyRate).HasColumnType("decimal(18, 8)");
```

Then entity

```cs
    public partial class CustomerCustomerRoleMapping : BaseEntity
    {
        /// <summary>
        /// Gets or sets the customer identifier
        /// </summary>
        public int CustomerId { get; set; }

        /// <summary>
        /// Gets or sets the customer role identifier
        /// </summary>
        public int CustomerRoleId { get; set; }

        /// <summary>
        /// Gets or sets the customer
        /// </summary>
        public virtual Customer Customer { get; set; }

        /// <summary>
        /// Gets or sets the customer role
        /// </summary>
        public virtual CustomerRole CustomerRole { get; set; }
    }
```

Now creating the relationship

```cs
    builder.HasKey(mapping => new { mapping.CustomerId, mapping.CustomerRoleId });

    builder.Property(mapping => mapping.CustomerId).HasColumnName("Customer_Id");
    builder.Property(mapping => mapping.CustomerRoleId).HasColumnName("CustomerRole_Id");

    builder.HasOne(mapping => mapping.Customer)
        .WithMany(customer => customer.CustomerCustomerRoleMappings)
        .HasForeignKey(mapping => mapping.CustomerId)
        .IsRequired();

    builder.HasOne(mapping => mapping.CustomerRole)
        .WithMany()
        .HasForeignKey(mapping => mapping.CustomerRoleId)
        .IsRequired();
```

Cascaded Delete

```cs
    builder.HasOne(order => order.BillingAddress)
        .WithMany()
        .HasForeignKey(order => order.BillingAddressId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
```
