unit MainUnit;

interface

uses
  Windows, Messages, SysUtils, Variants, Classes, Graphics, Controls, Forms,
  Dialogs, RegExpr, StdCtrls;

type
  TForm1 = class(TForm)
    Memo_input: TMemo;
    Button_ok: TButton;
    Button_input: TButton;
    OpenDialog: TOpenDialog;
    Memo_output: TMemo;
    procedure Button_inputClick(Sender: TObject);
    procedure Button_okClick(Sender: TObject);
    function deletefewlinescomment(var code:string):string;
    procedure deletecommentline(var code:string);
    procedure Deleteconststring(var code: string);
  private
    { Private declarations }
  public
    { Public declarations }
  end;
  Array_of_Variables = array of string;
  Values_Counter = array of integer;
var
  Form1: TForm1;
implementation

{$R *.dfm}

procedure TForm1.Button_inputClick(Sender: TObject);
begin
  if OpenDialog.Execute then
  begin
    Memo_output.Clear;
    Memo_input.Clear;
    Memo_input.Lines.LoadFromFile(OpenDialog.FileName);
  end;
end;

procedure SearchVars(var values : Array_of_Variables; var lengtharray : integer; code : string);
var
  Regular_Expression : TRegExpr;
  i:integer;
  Flag_to_Repeat:Boolean;
  temporary_variable_name:string;
begin
  Regular_Expression:= TRegExpr.Create;
  Regular_Expression.Expression :='[_a-zA-Z][_a-zA-Z0-9]*\s*\=';
  Flag_to_Repeat:=True;
  if Regular_Expression.Exec(code) then
  begin
    temporary_variable_name:=Copy(Regular_Expression.Match[0],1,Length(Regular_Expression.Match[0])-1);
    if temporary_variable_name[Length(temporary_variable_name)] = ' ' then
      while temporary_variable_name[Length(temporary_variable_name)]=' ' do
        Delete(temporary_variable_name, Length(temporary_variable_name), 1);
    for i:=0 to lengtharray-1 do
      if temporary_variable_name=values[i] then
        Flag_to_repeat:=False;
    if Flag_to_Repeat then
    begin
      inc(lengtharray);
      SetLength(values, lengtharray);
      values[lengtharray - 1] := temporary_variable_name;
    end;
  end;
  Regular_Expression.Free;
end;
function spen(Global : string; code:string):Integer;
var
  RegExpro : TRegExpr;
  i:Integer;
begin
  RegExpro := TRegExpr.Create;
  RegExpro.Expression :='\b'+global+'\b';
  if RegExpro.Exec(code) then
    Result:=1
    else
    Result:=0;
    RegExpro.Free;
end;
procedure TForm1.Deleteconststring(var code: string);
var
temporary_position:Integer;
begin
if Pos(#39,code)<>0 then
begin
temporary_position:=Pos(#39,code);
Delete(code,temporary_position,1);
while code[temporary_position]<>#39 do
Delete(code,temporary_position,1);
Delete(code,temporary_position,1);
end;
end;

procedure TForm1.deletecommentline(var code:string);
var
temporary_position:Integer;
begin
if Pos('#',code)<>0 then
begin
temporary_position:=Pos('#',code);
Delete(code,temporary_position,Length(code)-temporary_position+1);
end;
end;

function TForm1.deletefewlinescomment(var code:string):string;
var
i,j:Integer;
RegExpr:TRegExpr;
begin
RegExpr := TRegExpr.Create;
RegExpr.Expression :='['''']{3}';
if RegExpr.Exec(code) then
begin
repeat
i:=RegExpr.MatchPos[0];
if RegExpr.ExecNext then
begin
j:=RegExpr.MatchPos[0];
Delete(code,i,j-i+3);
end
else
Delete(code,i,3);
until not RegExpr.Exec(code);
end;
Result:=code;
end;


procedure TForm1.Button_okClick(Sender: TObject);
var
  Global : Array_of_Variables;
  local: Array_of_Variables;
  spen_global_variables,spen_local_variables:Values_Counter;
  i,j,Length_Global_Array,Length_Local_Array,start_function , initial_position: integer;
  temporary_code: string;
  all_spen:integer;
  flag_for_start_function:Boolean;
begin

  start_function:=0;
  Length_Global_Array:= 0;
  Length_Local_Array:= 0;
  flag_for_start_function:=false;
  temporary_code:=Memo_input.Text;
  Memo_input.Text:=deletefewlinescomment(temporary_code);
  for i:=0 to Memo_input.Lines.Count-1 do
  begin
    temporary_code:=Memo_input.Lines[i];
    Deleteconststring(temporary_code);
    deletecommentline(temporary_code);
    Memo_input.Lines[i]:=temporary_code;
  end;
  for i:=0 to memo_input.Lines.Count-1 do
  begin
    temporary_code:=Memo_input.Lines[i];
    if flag_for_start_function then
      if temporary_code[initial_position]<>' ' then
        flag_for_start_function:=False;
    if Pos('def ', temporary_code)<>0 then
    begin
      flag_for_start_function:=True;
      initial_position:=Pos('def ', temporary_code);
    end;
    if not flag_for_start_function then
      SearchVars(Global, Length_Global_Array,temporary_code)
    else
      SearchVars(local, Length_Local_Array,temporary_code);
  end;
  SetLength(spen_global_variables,Length_Global_Array);
  SetLength(spen_local_variables,Length_Local_Array);
  flag_for_start_function:=false;
  for i:=0 to Length_Global_Array-1 do
  begin

    spen_global_variables[i]:=0;
    for j:=0 to Memo_input.Lines.Count-1 do
    begin
      temporary_code:=Memo_input.Lines[j];
      if flag_for_start_function then
        if temporary_code[initial_position]<>' ' then
        flag_for_start_function:=False;
      if Pos('def ', temporary_code)<>0 then
      begin
        flag_for_start_function:=True;
        initial_position:=Pos('def ', temporary_code);
      end;
      if not flag_for_start_function then
        spen_global_variables[i]:=spen_global_variables[i]+spen(global[i], temporary_code);
    end;
  end;
  flag_for_start_function:=false;
  for i:=0 to Length_Local_Array-1 do
  begin
    spen_local_variables[i]:=0;
    for j:=0 to Memo_input.Lines.Count-1 do
    begin
      temporary_code:=Memo_input.Lines[j];
      if flag_for_start_function then
        if temporary_code[initial_position]<>' ' then
        flag_for_start_function:=False;
      if Pos('def ', temporary_code)<>0 then
      begin
        flag_for_start_function:=True;
        initial_position:=Pos('def ', temporary_code);
      end;
      if flag_for_start_function then
        spen_local_variables[i]:=spen_local_variables[i]+spen(local[i], temporary_code);
    end;
  end;
  Memo_output.Clear;
  all_spen:=0;
  Memo_output.Lines.Add('Глобальные переменные:');
  for i:=0 to Length_Global_Array-1 do
  begin
    Memo_output.Lines.Add(global[i]+': '+inttostr(spen_global_variables[i]-1) );
    inc(all_spen,spen_global_variables[i]-1);
  end;
  Memo_output.Lines.Add('Локальные переменные:');
  for i:=0 to Length_Local_Array-1 do
  begin
    Memo_output.Lines.Add(local[i]+': '+inttostr(spen_local_variables[i]-1) );
    inc(all_spen,spen_local_variables[i]-1);
  end;
    Memo_output.Lines.Add('Общий спен: '+inttostr(all_spen) );
end;




end.


