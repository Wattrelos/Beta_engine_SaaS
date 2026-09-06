# Rotas SLIM (PHP) com suporte de idiomas e SEO
Estrutura da URL:
meusite.com.br/{lang}/{caminho traduzido}

Exemplo:
meusite.com.br/pt-br/categoria/produto
meusite.com.br/en-us/category/product

As rotas serão agrupadas em idiomas.
As páginas Twig serão hidratadas conforme o idioma selecionado pelo usuário, carregando a lista de rotas do Locales
O roteaador SLIM receberá a requisições no idioma escolhido e encaminhará para o Action correspondente, por exemplo, ao receber a requisição /pt-br/categoria/produto/254 o roteador SLIM encaminhará para o Action \Alpha\Controller\Actions\Catalog\Product\ShowProductAction::class. 


| Method | Path | Action | Name |
| :--- | :--- | :--- | :--- |
| GET | '/setup' | \Alpha\Controller\Actions\Setup\ShowSetupAction::class | 'setup.show' |
| POST | '/setup/test-db' | \Alpha\Controller\Actions\Setup\TestDatabaseConnectionAction::class | 'setup.test_db' |
| POST | '/setup/process' | \Alpha\Controller\Actions\Setup\ProcessInstallationAction::class | 'setup.process' |
***Grupo de rotas protegidas do painel administrativo*** 
| GET | '/' | \Alpha\Admin\Controllers\Actions\Auth\ShowLoginAction::class | 'admin.login.form' |
| POST | '/login' | \Alpha\Admin\Controllers\Actions\Auth\LoginAction::class | 'admin.login.submit' |
| GET | '/setup' | \Alpha\Admin\Controllers\Actions\Auth\ShowSetupAction::class | 'admin.setup.form' |
| POST | '/setup' | \Alpha\Admin\Controllers\Actions\Auth\SetupAction::class | 'admin.setup.submit' |
| GET | '/dashboard' | \Alpha\Admin\Controllers\Actions\Dashboard\ViewDashboardAction::class | 'admin.dashboard' |
| GET | '/produtos' | \Alpha\Admin\Controllers\Actions\Catalog\Product\ListProductsAction::class | 'admin.product.list' |
| MAP(['GET', 'POST']) | '/produtos/criar' | \Alpha\Admin\Controllers\Actions\Catalog\Product\CreateProductAction::class | 'admin.product.create' |
| GET | '/produtos/{id:[0-9]+}/editar' | \Alpha\Admin\Controllers\Actions\Catalog\Product\EditProductAction::class | 'admin.product.edit' |
| POST | '/produtos/{id:[0-9]+}/editar' | \Alpha\Admin\Controllers\Actions\Catalog\Product\UpdateProductAction::class | 'admin.product.update' |
| GET | '/produtos/{id:[0-9]+}/excluir' | \Alpha\Admin\Controllers\Actions\Catalog\Product\DeleteProductAction::class | 'admin.product.delete' |
**Categorias**
| get | '/categorias' | \Alpha\Admin\Controllers\Actions\Catalog\Category\ListCategoriesAction::class | 'admin.category.list' |
| map(['GET', 'POST']) | '/categorias/criar' | \Alpha\Admin\Controllers\Actions\Catalog\Category\CreateCategoryAction::class | 'admin.category.create' |
| get | '/categorias/{id:[0-9]+}/editar' | \Alpha\Admin\Controllers\Actions\Catalog\Category\EditCategoryAction::class | 'admin.category.edit' |
| post | '/categorias/{id:[0-9]+}/editar' | \Alpha\Admin\Controllers\Actions\Catalog\Category\UpdateCategoryAction::class | 'admin.category.update' |
| get | '/categorias/{id:[0-9]+}/excluir' | \Alpha\Admin\Controllers\Actions\Catalog\Category\DeleteCategoryAction::class | 'admin.category.delete' |
***Fabricantes***
| get | '/fabricantes' | \Alpha\Admin\Controllers\Actions\Catalog\Manufacturer\ListManufacturersAction::class | 'admin.manufacturer.list' |
| get | '/fabricantes/criar' | \Alpha\Admin\Controllers\Actions\Catalog\Manufacturer\CreateManufacturerAction::class | 'admin.manufacturer.create' |
| post | '/fabricantes/criar' | \Alpha\Admin\Controllers\Actions\Catalog\Manufacturer\StoreManufacturerAction::class | 'admin.manufacturer.store' |
| get | '/fabricantes/{id:[0-9]+}/editar' | \Alpha\Admin\Controllers\Actions\Catalog\Manufacturer\EditManufacturerAction::class | 'admin.manufacturer.edit' |
| post | '/fabricantes/{id:[0-9]+}/editar' | \Alpha\Admin\Controllers\Actions\Catalog\Manufacturer\UpdateManufacturerAction::class | 'admin.manufacturer.update' |
| get | '/fabricantes/{id:[0-9]+}/excluir' | \Alpha\Admin\Controllers\Actions\Catalog\Manufacturer\DeleteManufacturerAction::class | 'admin.manufacturer.delete' |
***Fornecedores***
| get | '/fornecedores' | \Alpha\Admin\Controllers\Actions\Procurement\Supplier\ListSuppliersAction::class | 'admin.supplier.list' |
| get | '/fornecedores/criar' | \Alpha\Admin\Controllers\Actions\Procurement\Supplier\CreateSupplierAction::class | 'admin.supplier.create' |
| post | '/fornecedores/criar' | \Alpha\Admin\Controllers\Actions\Procurement\Supplier\StoreSupplierAction::class | 'admin.supplier.store' |
| get | '/fornecedores/{id:[0-9]+}/editar' | \Alpha\Admin\Controllers\Actions\Procurement\Supplier\EditSupplierAction::class | 'admin.supplier.edit' |
| post | '/fornecedores/{id:[0-9]+}/editar' | \Alpha\Admin\Controllers\Actions\Procurement\Supplier\UpdateSupplierAction::class | 'admin.supplier.update' |
| get | '/fornecedores/{id:[0-9]+}/excluir' | \Alpha\Admin\Controllers\Actions\Procurement\Supplier\DeleteSupplierAction::class | 'admin.supplier.delete' |
***Clientes***
| get | '/clientes' | \Alpha\Admin\Controllers\Actions\Customer\Customer\ListCustomersAction::class | 'admin.customer.list' |
| map(['GET', 'POST']) | '/clientes/criar' | \Alpha\Admin\Controllers\Actions\Customer\Customer\CreateCustomerAction::class | 'admin.customer.create' |
| map(['GET', 'POST']) | '/clientes/{id:[0-9]+}/editar' | \Alpha\Admin\Controllers\Actions\Customer\Customer\EditCustomerAction::class | 'admin.customer.edit' |
| get | '/clientes/{id:[0-9]+}' | \Alpha\Admin\Controllers\Actions\Customer\Customer\ShowCustomerAction::class | 'admin.customer.show' |
***Endereços de Clientes***
| map(['GET', 'POST']) | '/clientes/{customer_id:[0-9]+}/enderecos/criar' | \Alpha\Admin\Controllers\Actions\Customer\Address\CreateAddressAction::class | 'admin.customer.address.create' |
| map(['GET', 'POST']) | '/clientes/{customer_id:[0-9]+}/enderecos/{id:[0-9]+}/editar' | \Alpha\Admin\Controllers\Actions\Customer\Address\EditAddressAction::class | 'admin.customer.address.edit' |
| get | '/clientes/{customer_id:[0-9]+}/enderecos/{id:[0-9]+}/excluir' | \Alpha\Admin\Controllers\Actions\Customer\Address\DeleteAddressAction::class | 'admin.customer.address.delete' |
***Configurações da Loja***
| get | '/configuracoes' | \Alpha\Admin\Controllers\Actions\Setting\StoreSetting\EditStoreSettingAction::class | 'admin.setting.edit' |
| post | '/configuracoes' | \Alpha\Admin\Controllers\Actions\Setting\StoreSetting\UpdateStoreSettingAction::class | 'admin.setting.update' |
| get | '/configuracoes/informacoes/{id:[0-9]+}/excluir' | \Alpha\Admin\Controllers\Actions\Setting\StoreSetting\DeleteInformationAction::class | 'admin.setting.information.delete' |
***Gestão de Pedidos (Vendas)***
| get | '/pedidos' | \Alpha\Admin\Controllers\Actions\Sales\Order\ListOrdersAction::class | 'admin.orders.index' |
| get | '/pedidos/{id:[0-9]+}' | \Alpha\Admin\Controllers\Actions\Sales\Order\ShowOrderAction::class | 'admin.orders.show' |
| get | '/pedidos/{id:[0-9]+}/fatura' | \Alpha\Admin\Controllers\Actions\Sales\Order\ViewOrderDetailsAction::class | 'admin.orders.invoice' |
| post | '/pedidos/{id:[0-9]+}/status' | \Alpha\Admin\Controllers\Actions\Sales\Order\UpdateOrderStatusAction::class | 'admin.orders.update_status' |
***Gestão de Devoluções (Vendas)***
| get | '/devolucoes' | \Alpha\Admin\Controllers\Actions\Sales\Return\ListReturnsAction::class | 'admin.returns.index' |
| get | '/devolucoes/{id:[0-9]+}' | \Alpha\Admin\Controllers\Actions\Sales\Return\ShowReturnAction::class | 'admin.returns.show' |
| post | '/devolucoes/{id:[0-9]+}/status' | \Alpha\Admin\Controllers\Actions\Sales\Return\UpdateReturnStatusAction::class | 'admin.returns.update_status' |
***Gestão de Funcionários (Usuários Admin)***
| get | '/usuarios' | \Alpha\Admin\Controllers\Actions\User\User\ListUsersAction::class | 'admin.user.list' |
| map(['GET', 'POST']) | '/usuarios/criar' | \Alpha\Admin\Controllers\Actions\User\User\CreateUserAction::class | 'admin.user.create' |
| map(['GET', 'POST']) | '/usuarios/{id:[0-9]+}/editar' | \Alpha\Admin\Controllers\Actions\User\User\EditUserAction::class | 'admin.user.edit' |
| get | '/usuarios/{id:[0-9]+}/excluir' | \Alpha\Admin\Controllers\Actions\User\User\DeleteUserAction::class | 'admin.user.delete' |
***Gestão de Papéis (Grupos de Permissão)***
| get | '/papeis' | \Alpha\Admin\Controllers\Actions\User\UserGroup\ListUserGroupsAction::class | 'admin.user_group.list' |
| map(['GET', 'POST']) | '/papeis/criar' | \Alpha\Admin\Controllers\Actions\User\UserGroup\CreateUserGroupAction::class | 'admin.user_group.create' |
| map(['GET', 'POST']) | '/papeis/{id:[0-9]+}/editar' | \Alpha\Admin\Controllers\Actions\User\UserGroup\EditUserGroupAction::class | 'admin.user_group.edit' |
| get | '/papeis/{id:[0-9]+}/excluir' | \Alpha\Admin\Controllers\Actions\User\UserGroup\DeleteUserGroupAction::class | 'admin.user_group.delete' |
| get | '/logout' | \Alpha\Admin\Controllers\Actions\Auth\LogoutAction::class | 'admin.logout' |
| post | '/idioma' | \Alpha\Admin\Controllers\Actions\Common\SwitchAdminLanguageAction::class | 'admin.language.switch' |
***PDV (POS) Rotas do Vendedor***
| get | '/pos/vendedor' | \Alpha\Admin\Controllers\Actions\POS\ShowSalesRepDashboardAction::class | 'admin.pos.sales_rep' |
| get | '/pos/vendedor/checkout' | \Alpha\Admin\Controllers\Actions\POS\ShowSalesRepCheckoutAction::class | 'admin.pos.sales_rep.checkout' |
| get | '/pos/produtos/buscar' | \Alpha\Admin\Controllers\Actions\POS\SearchProductAction::class | 'admin.pos.products.search' |
| get | '/pos/clientes/buscar' | \Alpha\Admin\Controllers\Actions\POS\SearchCustomerAction::class | 'admin.pos.customers.search' |
| post | '/pos/pedidos/salvar' | \Alpha\Admin\Controllers\Actions\POS\CreatePreOrderAction::class | 'admin.pos.orders.save' |
// PDV (POS) Rotas do Caixa
| get | '/pos/caixa' | \Alpha\Admin\Controllers\Actions\POS\ShowCashierDashboardAction::class | 'admin.pos.cashier' |
| get | '/pos/pedidos/{id:[0-9]+}' | \Alpha\Admin\Controllers\Actions\POS\GetPreOrderAction::class | 'admin.pos.orders.get' |
| post | '/pos/pedidos/{id:[0-9]+}/pagar' | \Alpha\Admin\Controllers\Actions\POS\PayOrderAction::class | 'admin.pos.orders.pay' |
***1. REDIRECIONAMENTOS DE COMPATIBILIDADE / FALLBACKS DE IDIOMA***
// Redirecionamentos para o idioma padrão
| get | '/' | RedirectToDefaultLanguageAction::class | - |
| get | '/login' | RedirectToDefaultLanguageAction::class | - |
| get | '/cadastro' | RedirectToDefaultLanguageAction::class | - |
| get | '/logout' | RedirectToDefaultLanguageAction::class | - |
| get | '/carrinho' | RedirectToDefaultLanguageAction::class | - |
| get | '/busca' | RedirectToDefaultLanguageAction::class | - |
| map(['GET', 'POST']) | '/contato' | RedirectToDefaultLanguageAction::class | - |
| map(['GET', 'POST']) | '/contact' | RedirectToDefaultLanguageAction::class | - |
| map(['GET', 'POST']) | '/recuperar-senha' | RedirectToDefaultLanguageAction::class | - |
| map(['GET', 'POST']) | '/resetar-senha' | RedirectToDefaultLanguageAction::class | - |
| map(['GET', 'POST']) | '/checkout' | RedirectToDefaultLanguageAction::class | - |
| get | '/account' | RedirectToDefaultLanguageAction::class | - |
| get | '/account/orders' | RedirectToDefaultLanguageAction::class | - |
| get | '/account/order' | RedirectToDefaultLanguageAction::class | - |
| get | '/account/order/history/{order_id}' | RedirectToDefaultLanguageAction::class | - |
| get | '/account/addresses' | RedirectToDefaultLanguageAction::class | - |
| get | '/address' | RedirectToDefaultLanguageAction::class | - |
| get | '/address/create' | RedirectToDefaultLanguageAction::class | - |
| get | '/address/{address_id:[0-9]+}/edit' | RedirectToDefaultLanguageAction::class | - |
| get | '/address/{address_id:[0-9]+}/delete' | RedirectToDefaultLanguageAction::class | - |
| get | '/return' | RedirectToDefaultLanguageAction::class | - |
| get | '/wishlist' | RedirectToDefaultLanguageAction::class | - |
| get | '/edit' | RedirectToDefaultLanguageAction::class | - |
| get | '/password' | RedirectToDefaultLanguageAction::class | - |
| map(['GET', 'POST']) | '/resetar-senha' | RedirectToDefaultLanguageAction::class | - |
| get | '/transaction' | RedirectToDefaultLanguageAction::class | - |
| get | '/transactions' | RedirectToDefaultLanguageAction::class | - |
***2. ROTA DE REDIMENSIONAMENTO E CACHE DINÂMICO DE IMAGENS***
| get | '/image/cache/{path:.+}'' | \Alpha\Controller\Actions\Common\ImageCacheAction::class | - |
***3. APIs INTERNAS DA APLICAÇÃO***
| post | '/carrinho/dados' | CalculateVisitorCartAction::class | - |
| post | '/carrinho/sincronizar' | SyncCartAction::class | - |
| get | '/paises/{country_id:[0-9]+}/estados' | GetZonesAction::class | - |
| get | '/geo/paises/{country_id:[0-9]+}/estados' | \Alpha\Controller\Actions\Location\GetGeoZonesAction::class | - |
| get | '/geo/estados/{zone_id:[0-9]+}/cidades' | \Alpha\Controller\Actions\Location\GetGeoCitiesAction::class | - |
| post | '/carrinho/salvar-cep' | \Alpha\Controller\Actions\Cart\SaveShippingCepAction::class | - |
***Cotações e Levantamentos de Materiais (RFQ/BoQ)***
| get | '/meus-projetos' | \Alpha\Controller\Actions\Quotation\Customer\GetCustomerProjectsJsonAction::class | - |
| post | '/adicionar-item' | \Alpha\Controller\Actions\Quotation\Customer\AddProductToQuoteAction::class | - |
| get | '/produtos/buscar-takeoff' | \Alpha\Controller\Actions\Quotation\Provider\SearchCatalogItemsAction::class | - |
***Webhooks protegidos por validação HMAC SHA-256***
| post | '/{provider}' | callback function | - |
***4. GRUPO DE ROTAS INTERNACIONALIZADAS***
| $app->group | /{lang} (pt-br\|en\|es)|  function (RouteCollectorProxy $group) use ($authRateLimiter, $app) {
***Cache dinâmico de imagens acessado com prefixo de idioma***
| get | '/image/cache/{path:.+}' | \Alpha\Controller\Actions\Common\ImageCacheAction::class | - |
***Página Inicial do Idioma***
| get | '' | HomeAction::class | 'home' |
***Login***
| get | '/login' | ShowLoginFormAction::class | 'login.form' |
| post | '/login' | LoginAction::class | 'login.submit' |
***Recuperar e Resetar Senha***
| map(['GET', 'POST']) | '/recuperar-senha' | RequestPasswordResetAction::class | 'account.recuperar-senha' |
| map(['GET', 'POST']) | '/resetar-senha' | ResetPasswordAction::class | 'account.resetar-senha' |
***Cadastro***
| get | '/cadastro' | ShowRegistrationFormAction::class | 'register.form' |
| post | '/cadastro' | RegisterAction::class | 'register.submit' |
***Logout***
| get | '/logout' | LogoutAction::class | 'logout' |
***Grupo Protegido***
|    group('/account', function (RouteCollectorProxy $account) {
| get | '' | AccountAction::class | 'account.index' |
| get | '/orders' | OrdersAction::class | 'account.orders' |
| get | '/order/history/{order_id}' | OrderHistoryAction::class | 'account.order.history' |
| get | '/return' | ProductReturnsAction::class | 'account.returns' |
| map(['GET', 'POST']) | '/return/add' | \Alpha\Controller\Actions\Customer\Account\AddReturnAction::class | 'account.returns.add' |
| get | '/return/{id:[0-9]+}' | \Alpha\Controller\Actions\Customer\Account\ShowReturnAction::class | 'account.returns.show' |
***Editar Conta***
| get | '/edit' | \Alpha\Controller\Actions\Customer\Account\UpdateAction::class | 'account.edit' |
| post | '/edit' | \Alpha\Controller\Actions\Customer\Account\UpdateAction::class | - |
| map(['GET', 'POST']) | '/resetar-senha' | ResetPasswordAction::class | 'account.resetar-senha.logged' |
***Newsletter***
| post | '/newsletter' | \Alpha\Controller\Actions\Customer\Account\NewsletterAction::class | 'account.newsletter' |
***Transações***
| get | '/transaction' | \Alpha\Controller\Actions\Customer\Account\TransactionAction::class | 'account.transaction' |
***Lista de Desejos (Wishlist)***
| get | '/wishlist' | \Alpha\Controller\Actions\Customer\Account\WishlistAction::class | 'account.wishlist' |
| post | '/wishlist/add' | \Alpha\Controller\Actions\Customer\Account\WishlistAddAction::class | 'account.wishlist.add' |
| get | '/wishlist/remove/{product_id:[0-9]+}' | \Alpha\Controller\Actions\Customer\Account\WishlistRemoveAction::class | 'account.wishlist.remove' |
***Endereços***
| get | '/addresses' | ShowAddressesAction::class | 'account.addresses' |
| get | '/address/create' | CreateAddressAction::class | 'account.address.create' |
| post | '/address/create' | CreateAddressAction::class | - |
| get | '/address/{address_id:[0-9]+}/edit' | EditAddressAction::class | 'account.address.edit' |
| post | '/address/{address_id:[0-9]+}/edit' | EditAddressAction::class | - |
| get | '/address/{address_id:[0-9]+}/delete' | DeleteAddressAction::class | 'account.address.delete' |
***Aliases de Compatibilidade***
| get | '/address' | callback function (Redirect) | - |
| get | '/password' | callback function (Redirect) | - |
| get | '/order' | callback function (Redirect) | - |
| get | '/transactions' | callback function (Redirect) | - |
***Cotações e Projetos do Cliente (RFQ / BoQ)***
| get | '/projetos' | \Alpha\Controller\Actions\Quotation\Customer\ListCustomerProjectsAction::class | 'account.projects' |
| get | '/projetos/{rfq_id:[0-9]+}/propostas' | \Alpha\Controller\Actions\Quotation\Customer\ShowBidComparisonAction::class | 'account.projects.bids' |
| get | '/projetos/{rfq_id:[0-9]+}/propostas/{bid_id:[0-9]+}/aceitar' | \Alpha\Controller\Actions\Quotation\Customer\AcceptBidAction::class | 'account.projects.bids.accept' |
| map(['GET', 'POST']) | '/projetos/{rfq_id:[0-9]+}/boq' | \Alpha\Controller\Actions\Quotation\Customer\ApproveBoqAndAddToCartAction::class | 'account.projects.boq' |
})->add(new SessionMiddleware($group->getContainer()));
***Criação de Projetos e Solicitação de Orçamento (RFQ)***
| map(['GET', 'POST']) | '/projetos/novo' | \Alpha\Controller\Actions\Quotation\Customer\CreateProjectRfqAction::class | 'projects.create' |
| post | '/api/projetos/adicionar-item' | \Alpha\Controller\Actions\Quotation\Customer\AddProductToQuoteAction::class | 'api.projects.add_item' |
| get | '/api/projetos/meus-projetos' | \Alpha\Controller\Actions\Quotation\Customer\GetCustomerProjectsJsonAction::class | 'api.projects.my_projects' |
***Portal do Prestador de Serviços (Matching, Bids e Takeoff Tool)***
| get | '/prestador/oportunidades' | \Alpha\Controller\Actions\Quotation\Provider\ListOpportunitiesAction::class | 'provider.opportunities' |
| map(['GET', 'POST']) | '/prestador/projetos/{rfq_id:[0-9]+}/proposta' | \Alpha\Controller\Actions\Quotation\Provider\SubmitBidAction::class | 'provider.projects.bid' |
| map(['GET', 'POST']) | '/prestador/projetos/{rfq_id:[0-9]+}/takeoff' | \Alpha\Controller\Actions\Quotation\Provider\MaterialTakeoffAction::class | 'provider.projects.takeoff' |
| get | '/api/produtos/buscar-takeoff' | \Alpha\Controller\Actions\Quotation\Provider\SearchCatalogItemsAction::class | 'api.takeoff.search_products' |
***Detalhe do Produto, Categoria e Institucional (SEO)***
| get | '/produto/{slug}' | ShowProductAction::class | 'product.detail' |
| get | '/categoria/{slug}' | ShowCategoryAction::class | 'category.detail' |
| get | '/pagina/{slug}' | ShowInformationAction::class | 'info.page' |
***Mapa do Site (Sitemap)***
| get | '/mapa-do-site' | ShowSitemapAction::class | 'sitemap' |
| get | '/sitemap' | ShowSitemapAction::class | - |
| get | '/informacao/sitemap' | ShowSitemapAction::class | - |
| get | '/information/sitemap' | ShowSitemapAction::class | - |
| get | '/infomation/sitemap' | ShowSitemapAction::class | - |
***Contato (Contact)***
| map(['GET', 'POST']) | '/contato' | ShowContactAction::class | 'contact' |
| map(['GET', 'POST']) | '/contact' | ShowContactAction::class | - |
| map(['GET', 'POST']) | '/informacao/contato' | ShowContactAction::class | - |
| map(['GET', 'POST']) | '/information/contact' | ShowContactAction::class | - |
***Busca de Produtos***
| get | '/busca' | SearchAction::class | 'search' |
***Carrinho de Compras***
| get | '/carrinho' | ShowCartAction::class | 'cart.index' |
| post | '/carrinho/adicionar' | AddCartAction::class | 'cart.add' |
| post | '/carrinho/editar' | EditCartAction::class | 'cart.edit' |
| get | '/carrinho/remover/{key}' | RemoveCartAction::class | 'cart.remove' |
***Checkout***
| get | '/checkout' | \Alpha\Controller\Actions\Cart\Checkout::class | 'checkout.index' |
| post | '/checkout' | SubmitCheckoutAction::class | 'checkout.submit' |
| get | '/checkout/sucesso' | ShowSuccessAction::class | 'checkout.success' |



