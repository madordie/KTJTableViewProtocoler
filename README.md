 KTJTableViewProtocoler
//
//  KTJTableViewProtocoler.h
//  KTJTableViewProtocoler
//
//  Copyright (c) 2015年 Madordie. All rights reserved.
//


@protocol KTJTableViewProtocol;
@interface KTJTableViewProtocoler : NSObject

@property (nonatomic, weak) id<KTJTableViewProtocol> delegate;

//  这俩可以自己实现。。
- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView;
- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section;
//  优化请直接调用这两个方法。然后实现代理方法进行替换。
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath;
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath;

@end

//  如果使用必须实现这两个方法
@protocol KTJTableViewProtocol <NSObject>
/**
*  获取重用标识符
*
*  @param tableView  当前的TableView
*  @param dataSource 数据源
*  @param indexPath  位置
*
*  @return 当前cell的重用标识符
*/
- (NSString *)ktjtableView:(UITableView *)tableView reuseIdentifierWithDataSource:(id)dataSource indexPath:(NSIndexPath *)indexPath;
/**
*  使用数据源 填充cell
*
*  @param tableView  当前的TableView
*  @param cell       cell
*  @param dataSource 数据源
*  @param indexPath  位置
*/
- (void)ktjtableView:(UITableView *)tableView configureCell:(id)cell withDataSource:(id)dataSource indexPath:(NSIndexPath *)indexPath;

@end


@interface UITableView (KTJTableViewProtocoler)

@property (nonatomic, strong) KTJTableViewProtocoler *ktj_protocoler;

@property (nonatomic, strong) id ktj_dataSource;

@end



if 0

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

- (void)didReceiveMemoryWarning {
[super didReceiveMemoryWarning];
// Dispose of any resources that can be recreated.
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

endif