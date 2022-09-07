<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use Picqer\Barcode\BarcodeGeneratorPNG;
use Illuminate\Support\Facades\DB;
use App\Models\CheckStatus;
use Carbon\Carbon;
use Illuminate\Support\Facades\Storage;

class BarcodeGenerator extends Controller
{
    public function generateBarcode($id)
    {

        $limitVar=env('BARCODE_LIMIT');
        $checkStatus=CheckStatus::where([['module_id','=',$id]])->get();
        //print_r($checkStatus);die();
        if(count($checkStatus)==0)
        {
            $batchStart=0;
            $batchEnd=$limitVar;
        }
        else
        {
            $checkStatus1=CheckStatus::where([['module_id','=',$id]])->orderBy('id', 'DESC')->limit(1)->get();
            if($checkStatus1[0]['status'])
            {
                die();
            }
            $batchStartOld=$checkStatus1[0]['fromId'];
            $batchEndOld=$checkStatus1[0]['toId'];
            $batchStart=$batchEndOld;
            $batchEnd=$batchEndOld+$limitVar;
        }
        //
        $checkStatusInsert=new CheckStatus();
        $checkStatusInsert->fromId =$batchStart;
        $checkStatusInsert->toId=$batchEnd;
        $checkStatusInsert->status=1;
        $checkStatusInsert->startTime=Carbon::now();
        $checkStatusInsert->module_id=$id;
        $checkStatusInsert->save();

        $barcodeData=DB::table('barcode')->skip($batchStart)->take($limitVar)->get();
        //print_r($barcodeData);die();
        if(count($barcodeData)==0)
        {
            die();
        }
        foreach($barcodeData as $data)
        {
        	$barcode='';
        	$fileName=$data->file_name.'.png';
        	if($id=='1'){
        		$generatorPNG = new BarcodeGeneratorPNG();
            	$barcode=$generatorPNG->getBarcode($data->file_data, $generatorPNG::TYPE_CODE_128);
            	Storage::put('Barcode/'.$fileName, $barcode);  
        	}
        }
        $checkStatusAfter=CheckStatus::where([['module_id','=','1']])->orderBy('id', 'DESC')->limit(1)->get();
        $lastId=$checkStatusAfter[0]['id'];
        $checkStatusUpdate=CheckStatus::where([['id','=',$lastId],['status','=','1'],['module_id','=',$id]])->update(['status'=>'0','endTime'=>Carbon::now()]);
    }
}
