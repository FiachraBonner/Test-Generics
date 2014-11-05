unit MainF;

interface

uses
  Winapi.Windows, Winapi.Messages, System.SysUtils, System.Variants, System.Classes, Vcl.Graphics,
  Vcl.Controls, Vcl.Forms, Vcl.Dialogs, Vcl.StdCtrls;

type
  TForm1 = class(TForm)
    Button1: TButton;
    Memo1: TMemo;
    Button2: TButton;
    procedure Button1Click(Sender: TObject);
    procedure Button2Click(Sender: TObject);
  private
    { Private declarations }
  public
    { Public declarations }
  end;

var
  Form1: TForm1;

implementation

{$R *.dfm}

uses
  Generics.Collections;

type
  TNetworkElement = class
  public
    Id: Integer;
    Name: string;
  end;

  TBaseShape = class(TNetworkElement)
  private
    FKind: Integer;

  public
    property Kind: Integer read FKind write FKind;
  end;

  TNodeShape = class(TBaseShape)
  public
    X1: Integer;
  end;

  TElementList<T1:class;T2:class> = class(TObjectList<T1>)
  private
    function GetItem(i: Integer): T2;
  public
    procedure Add(A:T2);
    property Item[i:Integer]:T2 read GetItem; default;
  end;

  TBaseShapeList = TElementList<TNetworkElement, TBaseShape>;

procedure TElementList<T1, T2>.Add(A: T2);
begin
  // T1 = TNetworkElement, T2 = TNodeShape (for example), so:
  // A is TNodeShape and it is cast to TNetworkElement(T1) before being added.
  inherited Add(T1(TObject(A)));
end;

function TElementList<T1, T2>.GetItem(i: Integer): T2;
begin
  // T1 = TNetworkElement, T2 = TNodeShape (for example), so:
  // the TObject "item" is cast to TNodeShape for the Result.
  Result := T2(TObject(inherited Items[i]));
end;

procedure TForm1.Button1Click(Sender: TObject);
var
  ne: TNetworkElement;
  bs: TBaseShape;
  ns: TNodeShape;

  BaseShapes: TBaseShapeList; // TBaseShapeList = TElementList<TNetworkElement, TBaseShape>;
  NodeShapes: TElementList<TNetworkElement, TNodeShape>;
  Elements: TObjectList<TNetworkElement>;
  Elements2: TElementList<TNetworkElement, TNetworkElement>;

begin
  // Feed some dogs
  BaseShapes := TBaseShapeList.Create; // TElementList<TNetworkElement, TBaseShape>.Create;
  try
    bs := TBaseShape.Create;
    bs.Id := 10;
    bs.Name := 'BaseShape';
    bs.Kind := 1;

    BaseShapes.Add(bs);

    Memo1.Lines.Add(IntToStr(BaseShapes[0].Kind));

    NodeShapes := TElementList<TNetworkElement, TNodeShape>.Create(False);
    try
      ns := TNodeShape.Create;
      ns.Name := 'NodeShape';
      ns.X1 := 1234;

      NodeShapes.Add(ns);
      BaseShapes.Add(ns);

      Memo1.Lines.Add(IntToStr(NodeShapes[0].X1));
      Memo1.Lines.Add(BaseShapes[1].Name);
      Memo1.Lines.Add(IntToStr(TNodeShape(BaseShapes[1]).X1));

      Elements := TObjectList<TNetworkElement>.Create;
      try
        ne := TNetworkElement.Create;
        ne.Name := 'NetworkElement';

        // Looks like Elements can only add TNetworkElement objects and no derived objects, e.g.,
        // Elements.Add(bs) will give a runtime error.
        Elements.Add(ne);
        Memo1.Lines.Add(Elements.Items[0].Name);

        Elements2 := TElementList<TNetworkElement, TNetworkElement>.Create(False);
        try
          Elements2.Add(bs);
          Memo1.Lines.Add(Elements2.Items[0].Name);
        finally
          Elements2.Free;
        end;
      finally
        Elements.Free;
      end;
    finally
      NodeShapes.Free;
    end;
  finally
    BaseShapes.Free;
  end;

end;

type
  TBaseObject = class
  public
    Name: string;
  end;

  TDerivedObject = class(TBaseObject)
  public
    asdf: string;
  end;

  //TBaseList<T: TBaseObject> = class(TObjectList<T>)
  //T: TBaseObject is a "constraint" on TBaseList - i.e., we can only add objects of TBaseObject and its derived types to this list.
  TBaseList<T: TBaseObject> = class(TObjectList<T>)
  end;

procedure TForm1.Button2Click(Sender: TObject);
var
  bl: TBaseList<TBaseObject>;
  dl: TBaseList<TDerivedObject>;
  //bl: TObjectList<TBaseObject>;

begin
  bl := TBaseList<TBaseObject>.Create;
  try
    bl.Add(TBaseObject.Create);
    bl.Items[0].Name := 'BaseObject';

    Memo1.Lines.Add(bl[0].Name);

    bl.Add(TDerivedObject.Create);
    bl.Items[1].Name := 'DerivedObject';
    TDerivedObject(bl.Items[1]).asdf := 'asdf';

    Memo1.Lines.Add(bl[1].Name);
    Memo1.Lines.Add(TDerivedObject(bl[1]).asdf);

  finally
    bl.Free;
  end;

  dl := TBaseList<TDerivedObject>.Create;
  try
    {FB+NOTE: Cannot add a TBaseObject to this list as it is not derived from from TDerivedObject!
    ...In a "normal" TList, you could.
    dl.Add(TBaseObject.Create);
    dl.Items[0].Name := 'BaseObject';
    Memo1.Lines.Add(dl[0].Name);
    }

    dl.Add(TDerivedObject.Create);
    dl.Items[0].Name := 'DerivedObject';
    TDerivedObject(dl.Items[0]).asdf := 'asdf';

    Memo1.Lines.Add(dl[0].Name);
    Memo1.Lines.Add(TDerivedObject(dl[0]).asdf);

  finally
    dl.Free;
  end;
end;

end.
