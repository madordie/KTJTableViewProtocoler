# KTJTableViewProtocoler

简化UITableView复杂的优化以及重复的代理、数据源实现。

# 注意
使用FD做的优化，所以：
    如果Cell使用的是约束则可以忽略高度计算。
    如果Cell使用frame进行约束设置，请重写sizeThatFits:方法返回高度。
该方法分为两种模式：精简模式和普通模式。如果该TableView只需要：
```
- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView;
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section;
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath;
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath;
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath;
- (UIView *)tableView:(UITableView *)tableView viewForHeaderInSection:(NSInteger)section;
- (UIView *)tableView:(UITableView *)tableView viewForFooterInSection:(NSInteger)section;
- (CGFloat)tableView:(UITableView *)tableView heightForFooterInSection:(NSInteger)section;
- (CGFloat)tableView:(UITableView *)tableView heightForHeaderInSection:(NSInteger)section;
```
以上几种方法，则可直接设置self.tableView.ktj_simpleList = YES;进行简化代码。具体如下。
如果以上方法不足以完成展示。则请使用第二种，忽略self.tableView.ktj_simpleList = YES;
# 示例代码
```
//  精简模式
pragma mark - < *** ViewController    生命周期   👇 ***>

- (void)viewDidLoad {
[super viewDidLoad];
// Do any additional setup after loading the view.
self.tableView = [UITableView new];
self.tableView.frame = self.view.bounds;
[self.view addSubview:self.tableView];

[self.tableView registerClass:[UITableViewCell class] forCellReuseIdentifier:@"UITableViewCell"];
self.tableView.ktj_protocoler.delegate = self;
self.tableView.ktj_simpleList = YES;

self.tableView.ktj_dataSource = @[@"1", @"2", @"3", @"4"];
}

pragma mark - < *** ViewController      代理     👇 ***>
- (void)ktjtableView:(UITableView *)tableView configureCell:(UITableViewCell *)cell withDataSource:(id)dataSource indexPath:(NSIndexPath *)indexPath {
cell.textLabel.text = dataSource[indexPath.row];
}
- (NSString *)ktjtableView:(UITableView *)tableView reuseIdentifierWithDataSource:(id)dataSource indexPath:(NSIndexPath *)indexPath {
return @"UITableViewCell";
}
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
NSLog(@"哇哈哈");
}
```

```
//  精简模式几个方法无法完成需求，则需要使用如下方法进行设置。
- (void)viewDidLoad {
[super viewDidLoad];
// Do any additional setup after loading the view.
self.tableView = [UITableView new];
self.tableView.frame = self.view.bounds;
[self.view addSubview:self.tableView];

[self.tableView registerClass:[UITableViewCell class] forCellReuseIdentifier:@"UITableViewCell"];
self.tableView.delegate = self;
self.tableView.dataSource = self;
self.tableView.ktj_protocoler.delegate = self;

self.tableView.ktj_dataSource = @[@"1", @"2", @"3", @"4"];
}

pragma mark - < *** ViewController      代理     👇 ***>
//KTJ的方法不多只有两个，只实现了四个方法，其实只用调用最后两个就好。
- (void)ktjtableView:(UITableView *)tableView configureCell:(UITableViewCell *)cell withDataSource:(id)dataSource indexPath:(NSIndexPath *)indexPath {
cell.textLabel.text = dataSource[indexPath.row];
}
- (NSString *)ktjtableView:(UITableView *)tableView reuseIdentifierWithDataSource:(id)dataSource indexPath:(NSIndexPath *)indexPath {
return @"UITableViewCell";
}
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
return [tableView.ktj_protocoler tableView:tableView numberOfRowsInSection:section];
}
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
return [tableView.ktj_protocoler tableView:tableView cellForRowAtIndexPath:indexPath];
}
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath {
return [tableView.ktj_protocoler tableView:tableView heightForRowAtIndexPath:indexPath];
}
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
//tableView.ktj_protocoler 中没有的方法直接写就好啦！！
NSLog(@"哇哈哈");
}

```