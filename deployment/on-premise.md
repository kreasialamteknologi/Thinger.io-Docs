# On-Premise

## Subscribing and Deploying On-premise Instances

This section describres the proccess to deploy a private Thinger.io On-premise instance within minutes by just accessing the [**Pricing Page**](https://pricing.thinger.io) \(On-premise section\) in the "PRICING" link from our [Web Home](https://thinger.io). Just follow the next steps:

### 1. Select the right licence

On-premise instances can be deployed with different licenses, depending on the project requriements, mainly in terms of platform features like rebrands, custom domains, additional suppport, plugins, etc. On-premise licenses starts for free with the Maker license, but limited with some advanced features enabled in the following tiers, most notably, custom domains with automatic SSL support, rebranding, plugins, multiple server accounts, or extend the server capabilities with plugins like Node-Red. So, the first step is to select the required license, as shown in the image below:

![On-premise license selection](../.gitbook/assets/image%20%28156%29.png)

This pricing includes the software license to deploy an on-premise instance. Note that it is possible to select monthly or yearly license with a great discount. 

The next table shows the main different features that are provided by each license as well as a desirable purpose specification. It is possible to select one license and change it in the future using the [subscription management portal](https://thinger.chargebeeportal.com). 

|  | **MAKER** | **GROW** | **STARTUP** | **BUSINESS** |
| :--- | :--- | :--- | :--- | :--- |
| **Devices** | Unlimited | Unlimited | Unlimited | Unlimited |
| **Plugins** |  | 3 | 6 | Unlimited |
| **Rebranding** | \*\*\*\* | ✓ | ✓ | Included |
| **Custom Domain** | \*\*\*\* | ✓ | ✓ | Included |
| **Multiple Account** | \*\*\*\* | ✓ | ✓ | Included |
| **Extended support** | \*\*\*\* | ✓ | ✓ | ✓ |
| **Limited Performance** | ✓ |  |  |  |
| **HA Cluster** | \*\*\*\* |  |  |  |
| **Recommended network size** | Individual projects | &lt;10 user accounts. | 10 to 50 accounts |  &gt;50 user accounts |

### 2.  Configure license

Once the license has been selected, it is possible to customize the service with some options like additional users, support, custom domains, brands, etc. These options appers after selecting a plan, like shown in the figure below:

![Instance license preferences](../.gitbook/assets/image%20%2897%29.png)

These options are described in more detail in the following:

* **Admin Email**: This is the email address that must be used when creating the Thinger.io account in the private instance deployed. It will be the main account with admin privileges, allowing to create \(if contracted\) new users, domains, brands, etc.
* **Additional users**: This option allows to increase the amount of accounts \(in addition to the admin account\) in order to allow other users to log-in to your instance. It is possible to add as many accounts as required, however, a large user base will require a better host to support their databases, plugins, etc. 
* **Extended Support**: This option is recommended in order to obtain Thinger.io engineers development support with a 24-48h response time. All accounts can use the [community discussion](https://community.thinger.io) forum to obtain support from other community developers.
* **Custom Domains**: It is the amount of different web domains that can be redirected to the same Private Instance. The instance will automatically provision and renew SSL certificates to support the new defined domains. Domains are required for different brands, as they are associated to the domain name.
* **Custom Brands**: It is the amount of different console brands that can be created over the same instance. In each brand it is possible to customize some accent colors, logos, copyrights, contact emails, title, etc. Notice that each brand requires a custom domain, as the customization is done for each domain name.

### 3.  Checkout and payment options

After configuring the selected license, the checkout process is really simple, just click on the "Deploy Instance" button, and wait for the checkout pop-up. In the new form, it is necessary to introduce your billing email \(it can be different from the admin email\) where the invoices \(if subscribed to a paid plan\) will be sent:

![Billing email](../.gitbook/assets/image%20%28182%29.png)

After introducing the billing email, it is necessary to set the billing address. It is possible to set a VAT number if you are a registered company from the European Union in order to calculate the right taxes and build the invoice.

![Billing address](../.gitbook/assets/image%20%2890%29.png)

Finally, it is necessary to select the payment method between Credit Card or Direct Debit \(if subscribed to a paid plan\), that allows the domiciliation of the payment with SEPA tansferences. Once the payment process is finished, Thinger.io customer management system will automatically generate a server license that will be emailed to the admin email.

### 4.  On-premise install

Once you have received the license token, it is possible to easily deploy Thinger.io in your host with a few commands. Before you start this guide, please, [install Docker Engine](https://docs.docker.com/install/) in your computer or server.

{% hint style="success" %}
[Install Docker Engine](https://docs.docker.com/install/) before following this guide
{% endhint %}


