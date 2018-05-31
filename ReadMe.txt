Compiling GW_Chart requires making some changes to the source code of the TChart (version 6.01) component from Steema Software (http://www.steema.com).  The required changes are as follows.

In the interface section of TeEngine.pas add a new type after the declaration of TSeriesClick.  The new type is as follows.

  TOnDrawAxisLabel = procedure( Sender: TChartAxis; X, Y, Z: integer;
                                AxisLabel: string; var DrawLabel: boolean) of object;

In the interface section of TeEngine.pas in the private section of TCustomAxisPanel add the following.

    FOnDrawAxisLabel: TOnDrawAxisLabel;

In the interface section of TeEngine.pas in the protected section of TCustomAxisPanel add the following.

    property OnDrawAxisLabel: TOnDrawAxisLabel read FOnDrawAxisLabel write FOnDrawAxisLabel;

In the implementation section of TeEngine.pas in the implementation of procedure TChartAxis.DrawAxisLabel add the following new local variable at the end of the list of local variables.

    DrawLabel: boolean;

In the implementation section of TeEngine.pas in the implementation of procedure TChartAxis.DrawAxisLabel after the line "y:=y+Delta" replace 
        if FLabelsExponent then DrawExponentLabel
                           else ParentChart.Canvas.TextOut3D(X,Y,tmpZ,tmpSt2);

with 
      DrawLabel := True;
      if Assigned(ParentChart.OnDrawAxisLabel) then
      begin
        ParentChart.OnDrawAxisLabel( self, X, Y, tmpZ, tmpSt2, DrawLabel);
      end;
      if DrawLabel then
      begin
        if FLabelsExponent then DrawExponentLabel
                           else ParentChart.Canvas.TextOut3D(X,Y,tmpZ,tmpSt2);
      end

In the implementation section of TeEngine.pas in the implementation of procedure TChartAxis.DrawAxisLabel after the line "if (Angle=90) or (Angle=270) then x:=x+Delta;" replace 

        ParentChart.Canvas.RotateLabel3D(X,Y,tmpZ,tmpSt2,Angle);

with 
      DrawLabel := True;
      if Assigned(ParentChart.OnDrawAxisLabel) then
      begin
        ParentChart.OnDrawAxisLabel( self, X, Y, tmpZ, tmpSt2, DrawLabel);
      end;
      if DrawLabel then
      begin
        ParentChart.Canvas.RotateLabel3D(X,Y,tmpZ,tmpSt2,Angle);
      end

In the implementation section of TeEngine.pas change the body of Function TChartValueLists.Get from 

    result:=List^[Index];

to 

  if Index < 0 then
  begin
    result := nil;
  end
  else
  begin
    result:=List^[Index];
  end;


In the interface section of Chart.pas add the following to the published section of TChart.

    property OnDrawAxisLabel;

In the implementation section of TeEngine.pas in the implementation of procedure TSeriesPointer.PrepareCanvas

in the next to the last line replace
  ACanvas.AssignBrushColor(Brush,tmp,ColorValue);
with
  ACanvas.AssignBrushColor(Brush,ColorValue,tmp);

In TeeEditCha.pas change all instances of '.hlp' to '.chm'.

In TEngine.pas, in TSeriesPointer.DrawPointer, the code for drawing a cirle should be changed from 

                       EllipseWithZ(PXMinus,PYMinus,PXPlus,PYPlus,GetStartZ)
to
                       EllipseWithZ(PXMinus,PYMinus,PXPlus,PYPlus,GetMiddleZ)

In TEngine.pas, in TSeriesPointer.DrawPointer, the following lines in DrawDiagonalCross should be changed:
from
        LineWithZ(PXMinus, PYMinus, PXPlus+1,PYPlus+1,GetStartZ);
        LineWithZ(PXPlus,  PYMinus, PXMinus-1,PYPlus+1,GetStartZ);
to
        LineWithZ(PXMinus, PYMinus, PXPlus+1,PYPlus+1,GetMiddleZ);
        LineWithZ(PXPlus,  PYMinus, PXMinus-1,PYPlus+1,GetMiddleZ);

In TEngine.pas, in TSeriesPointer.DrawPointer, the following lines in DrawCross should be changed:
from
        VertLine3D(PX,PYMinus,PYPlus+1,GetStartZ);
        HorizLine3D(PXMinus,PXPlus+1,PY,GetStartZ);
to
        VertLine3D(PX,PYMinus,PYPlus+1,GetMiddleZ);
        HorizLine3D(PXMinus,PXPlus+1,PY,GetMiddleZ);

