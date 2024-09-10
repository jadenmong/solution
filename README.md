import java.util.ArrayList;
import java.util.List;

public class Main {
    // 定义体积重基数
    private static final int VOLUME_WEIGHT_BASE = 250;

    public List<String> test(double lengthCm, double widthCm, double heightCm, double weightKg) {
        // 将厘米转换为英寸，千克转换为磅，并向上取整
        double lengthInches = Math.ceil(lengthCm / 2.54);
        double widthInches = Math.ceil(widthCm / 2.54);
        double heightInches = Math.ceil(heightCm / 2.54);
        double weightLb = Math.ceil(weightKg / 0.454);

        // 找出最长边、次长边、第三边
        double[] edges = {lengthInches, widthInches, heightInches};
        double longest = Math.max(edges[0], Math.max(edges[1], edges[2]));
        double third = Math.min(edges[0], Math.min(edges[1], edges[2]));
        double middle = edges[0] + edges[1] + edges[2] - longest - third;

        // 计算围长（最长边 + 2 * (次长边 + 第三边)）
        double girth = longest + 2 * (middle + third);

        // 计算体积重并向上取整
        double volumeWeight = Math.ceil((longest * middle * third) / VOLUME_WEIGHT_BASE);

        // 实重是产品重量（LB）和体积重之间取较大值
        double actualWeight = Math.max(weightLb, volumeWeight);

        // 存放类型结果的列表
        List<String> types = new ArrayList<>();

        // 判断 OUT_SPACE 类型
        if (actualWeight > 150 || longest > 108 || girth > 165) {
            types.add("OUT_SPACE");
            return types;  // 满足 OUT_SPACE 时直接返回，不再判断其他类型
        }

        // 判断 OVERSIZE 类型
        if ((girth > 130 && girth <= 165) || (longest >= 96 && longest < 108)) {
            types.add("OVERSIZE");
            return types;  // 满足 OVERSIZE 时直接返回，不再判断 AHS
        }

        // 判断 AHS-WEIGHT 类型
        if (actualWeight > 50 && actualWeight <= 150) {
            types.add("AHS-WEIGHT");
        }

        // 判断 AHS-SIZE 类型
        if (girth > 105 || (longest >= 48 && longest < 108) || middle >= 30) {
            types.add("AHS-SIZE");
        }

        return types;  // 返回符合的所有类型
    }

    public static void main(String[] args) {
        Main obj = new Main();

        System.out.println(obj.test(68, 70, 60, 23));               // [AHS-WEIGHT, AHS-SIZE]
        System.out.println(obj.test(114.5, 42, 26, 47.5));          // [AHS-WEIGHT]
        System.out.println(obj.test(162, 60, 11, 14));              // [AHS-SIZE]
        System.out.println(obj.test(113, 64, 42.5, 35.85));         // [OVERSIZE]
        System.out.println(obj.test(114.5, 17, 51.5, 16.5));        // []
    }
}
