```dart
import 'package:flutter/material.dart';
import 'package:flutter/rendering.dart';
import 'package:flutter_business_env/custom_widget/cell/company_list_item.dart';
import 'package:flutter_business_env/custom_widget/child_gridView.dart';
import 'package:flutter_business_env/custom_widget/child_grid_bottomsheet.dart';
import 'package:flutter_business_env/custom_widget/custom_appbar.dart';
import 'package:flutter_business_env/custom_widget/fake_search_bar.dart';
import 'package:flutter_business_env/custom_widget/simple_dialog_component.dart';
import 'package:flutter_business_env/model/company/company_list_model.dart';
import 'package:flutter_business_env/pages/test_page.dart';
import 'package:flutter_business_env/utils/color_util.dart';
import 'package:flutter_business_env/utils/request/request.dart';
import 'package:flutter_easyrefresh/easy_refresh.dart';
import 'package:flutter_swiper/flutter_swiper.dart';
import 'package:flutter/material.dart';
import 'package:marquee/marquee.dart';
import 'company/company_detail_page.dart';
import 'company/creator_company_page.dart';
import 'form_page.dart';
import 'mine/mine_page.dart';

class HomePage extends StatefulWidget {
  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  List _imageList = [
    'https://shadow.elemecdn.com/app/element/hamburger.9cf7b091-55e9-11e9-a976-7f4d0b07eef6.png',
    'https://fuss10.elemecdn.com/e/5d/4a731a90594a4af544c0c25941171jpeg.jpeg',
    'https://fuss10.elemecdn.com/1/8e/aeffeb4de74e2fde4bd74fc7b4486jpeg.jpeg'
  ];

  bool _enableRefresh = true;
  // 是否开启加载
  bool _enableLoad = true;

  List _listData = List();
  int _page = 0;
  EasyRefreshController _controller;
  ScrollController _scrollController;
  // 连接通知器
  LinkHeaderNotifier _headerNotifier;

  _startRequestCompanyListApi() {
    _page = 0;
    _listData = List();
    Map<String, dynamic> params = Map();
    params['owner'] = true;
    params['page'] = _page;
    params['size'] = 10;
    RequestUtil.doCompanyList(params).then((result) {
      print(result);
      CompanyListModel companyListModel = CompanyListModel.fromMap(result);
      if (companyListModel.data.content.length > 0) {
        _page++;
        setState(() {
          _listData.addAll(companyListModel.data.content);
        });
        _controller.finishRefresh();
      } else {
        _controller.finishRefresh();
      }
    });
  }

  _startRequestMoreCompanyListApi() {
    Map<String, dynamic> params = Map();
    params['owner'] = true;
    params['page'] = _page;
    params['size'] = 10;
    RequestUtil.doCompanyList(params).then((result) {
      print(result);
      CompanyListModel companyListModel = CompanyListModel.fromMap(result);
      if (companyListModel.data.content.length > 0) {
        _page++;
        setState(() {
          _listData.addAll(companyListModel.data.content);
        });
        _controller.finishLoad(success: true);
      } else {
        _controller.resetLoadState();
        _controller.finishLoad(noMore: true);
      }
    });
  }

  @override
  void initState() {
    // TODO: implement initState
    super.initState();
    _controller = EasyRefreshController();
    _scrollController = ScrollController();
    _startRequestCompanyListApi();

    _headerNotifier = LinkHeaderNotifier();
  }

  @override
  void dispose() {
    super.dispose();
    _headerNotifier.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: ColorUtil.principalBackgroundColor,
        body: EasyRefresh.custom(
      header: LinkHeader(
        _headerNotifier,
        extent: 70.0,
        triggerDistance: 70.0,
        completeDuration: Duration(milliseconds: 500),
      ),
      footer: BallPulseFooter(),
      onRefresh: () async {
        await Future.delayed(Duration(seconds: 2), () {
          if (mounted) {
            _startRequestCompanyListApi();
          }
        });
      },
      onLoad: () async {
        await Future.delayed(Duration(seconds: 2), () {
          if (mounted) {
            _startRequestMoreCompanyListApi();
          }
        });
      },
      slivers: <Widget>[
        SliverAppBar(
          pinned: true,
          elevation: 0,
          expandedHeight: 200.0,
          backgroundColor: Colors.blue,
          title: SABT(child: Text("The title")),
          flexibleSpace: new FlexibleSpaceBar(
            background: Container(
              color: Colors.red,
              child: Swiper(
                itemBuilder: (BuildContext context, int index) {
                  return Image.network(
                    _imageList[index],
                    fit: BoxFit.fill,
                  );
                },
                autoplay: _imageList.length == 1 ? false : true,
                itemCount: _imageList.length,
                pagination: _imageList.length == 1
                    ? null
                    : SwiperPagination(builder: SwiperPagination.dots),
              ),
            ),
          ),
          actions: <Widget>[
            CircleHeader(
              _headerNotifier,
            ),
          ],
        ),
        SliverList(
          delegate: SliverChildBuilderDelegate(
            (context, index) {
              print(_listData[index]);
              ContentBean contentBean = _listData[index];

              if (index == 0) {
                return GestureDetector(
                  onTap: () {
//                    Navigator.of(context).push(MaterialPageRoute(
//                        builder: (BuildContext context) {
//                          return CompanyDetailPage(contentBean: contentBean,);
//                        })).then((value) {
//                      _startRequestCompanyListApi();
//                    });
                  },
                  child: Container(
                    color: Colors.white,
                    height: 80,

                    child: Row(
                      crossAxisAlignment: CrossAxisAlignment.center,
                      mainAxisAlignment: MainAxisAlignment.start,
                      children: [
                        SizedBox(
                          width: 20,
                        ),
                        Icon(Icons.notifications, color: Colors.orangeAccent,),
                        SizedBox(
                          width: 20,
                        ),
                        Container(
                          width: 250,
                          height: 50,
                          color: Colors.red,
                          child: Swiper(
                            itemBuilder: (BuildContext context, int index) {
                              return Container(
                                alignment: Alignment.centerLeft,
                                child: Text(
                                    'GridView.extent构造函数内部使用了'),
                              );
                            },
                            autoplay: _imageList.length == 1 ? false : true,
                            itemCount: _imageList.length,
                            pagination: null,
                            scrollDirection: Axis.vertical,
                          ),
                        )
                      ],
                    ),
                  ),
                );
              } else {
                return GestureDetector(
                  onTap: () {
                    Navigator.of(context).push(
                        MaterialPageRoute(builder: (BuildContext context) {
                      return CompanyDetailPage(
                        contentBean: contentBean,
                      );
                    })).then((value) {
                      _startRequestCompanyListApi();
                    });
                  },
                  child: CompanyListItem(
                    contentBean: contentBean,
                  ),
                );
              }
            },
            childCount: _listData.length,
          ),
        ),
      ],
    ));
  }
}

class SABT extends StatefulWidget {
  final Widget child;
  const SABT({
    Key key,
    @required this.child,
  }) : super(key: key);
  @override
  _SABTState createState() {
    return new _SABTState();
  }
}

class _SABTState extends State<SABT> {
  ScrollPosition _position;
  bool _visible;
  @override
  void dispose() {
    _removeListener();
    super.dispose();
  }

  @override
  void didChangeDependencies() {
    super.didChangeDependencies();
    _removeListener();
    _addListener();
  }

  void _addListener() {
    _position = Scrollable.of(context)?.position;
    _position?.addListener(_positionListener);
    _positionListener();
  }

  void _removeListener() {
    _position?.removeListener(_positionListener);
  }

  void _positionListener() {
    final FlexibleSpaceBarSettings settings =
        context.inheritFromWidgetOfExactType(FlexibleSpaceBarSettings);
    bool visible =
        settings == null || settings.currentExtent <= settings.minExtent;
    if (_visible != visible) {
      setState(() {
        _visible = visible;
      });
    }
  }

  @override
  Widget build(BuildContext context) {
    return Visibility(
      visible: _visible,
      child: widget.child,
    );
  }
}

// 圆形Header
class CircleHeader extends StatefulWidget {
  final LinkHeaderNotifier linkNotifier;

  const CircleHeader(this.linkNotifier, {Key key}) : super(key: key);

  @override
  CircleHeaderState createState() {
    return CircleHeaderState();
  }
}

class CircleHeaderState extends State<CircleHeader> {
  // 指示器值
  double _indicatorValue = 0.0;

  RefreshMode get _refreshState => widget.linkNotifier.refreshState;
  double get _pulledExtent => widget.linkNotifier.pulledExtent;

  @override
  void initState() {
    super.initState();
    widget.linkNotifier.addListener(onLinkNotify);
  }

  void onLinkNotify() {
    setState(() {
      if (_refreshState == RefreshMode.armed ||
          _refreshState == RefreshMode.refresh) {
        _indicatorValue = null;
      } else if (_refreshState == RefreshMode.refreshed ||
          _refreshState == RefreshMode.done) {
        _indicatorValue = 1.0;
      } else {
        if (_refreshState == RefreshMode.inactive) {
          _indicatorValue = 0.0;
        } else {
          double indicatorValue = _pulledExtent / 70.0 * 0.8;
          _indicatorValue = indicatorValue < 0.8 ? indicatorValue : 0.8;
        }
      }
    });
  }

  @override
  Widget build(BuildContext context) {
    return Center(
      child: Container(
        alignment: Alignment.center,
        margin: EdgeInsets.only(
          right: 20.0,
        ),
        width: 24.0,
        height: 24.0,
        child: CircularProgressIndicator(
          value: _indicatorValue,
          valueColor: AlwaysStoppedAnimation(Colors.white),
          strokeWidth: 2.4,
        ),
      ),
    );
  }
}
```

![4son6-9hs1d](https://raw.githubusercontent.com/xiaobeifeng/blog/main/image/4son6-9hs1d.gif)